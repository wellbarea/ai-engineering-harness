# Database

> **Template** — document your migration workflow, access control conventions,
> and database function patterns. Agents read this for all DB work.

## Migration Workflow

> Describe your migration tooling and the steps to create/apply a migration.

```bash
# [YOUR MIGRATION COMMANDS]

# Example for Supabase:
# 1. Create a new migration file
supabase migration new <descriptive-name>

# 2. Write SQL in the generated file
# supabase/migrations/<timestamp>_<name>.sql

# 3. Apply locally (if Docker available)
supabase db reset

# 4. Apply to remote
supabase db push --linked

# 5. Regenerate TypeScript types
pnpm db:types
```

## Naming Conventions

| Object | Convention | Example |
|--------|-----------|---------|
| Tables | snake_case plural | `products`, `order_items` |
| Columns | snake_case | `created_at`, `tenant_id` |
| Functions/RPCs | snake_case verb_noun | `create_order`, `close_register` |
| Indexes | `idx_<table>_<column>` | `idx_products_tenant_id` |
| Policies | `<role> can <action> <table>` | `authenticated can read products` |

## Access Control (RLS)

> Document your multi-tenancy and access control approach.

```sql
-- Every new table gets RLS enabled and policies in the SAME migration
-- Never defer access control to a follow-up migration

ALTER TABLE new_table ENABLE ROW LEVEL SECURITY;

-- Example: tenant isolation
CREATE POLICY "tenant isolation" ON new_table
  FOR ALL USING (tenant_id = auth.jwt() ->> 'tenant_id');

-- Example: role-based access
CREATE POLICY "managers can write" ON new_table
  FOR INSERT WITH CHECK (
    (auth.jwt() ->> 'role') = 'manager'
  );
```

## Database Functions / RPCs

> Use a database function (stored procedure) when an operation:
> - touches multiple tables
> - must be atomic
> - enforces a domain invariant that can't be safely expressed in the app layer

```sql
-- Example RPC pattern
CREATE OR REPLACE FUNCTION create_order(
  p_tenant_id UUID,
  p_items JSONB
) RETURNS UUID
LANGUAGE plpgsql
SECURITY DEFINER   -- runs as owner, not caller (for RLS bypass where needed)
SET search_path = public
AS $$
DECLARE
  v_order_id UUID;
BEGIN
  -- Your atomic operation here
  INSERT INTO orders (tenant_id) VALUES (p_tenant_id) RETURNING id INTO v_order_id;
  
  -- Insert order items, check inventory, etc.
  -- Everything in one transaction — if any step fails, all roll back
  
  RETURN v_order_id;
END;
$$;
```

## Common Patterns

### Soft Delete

```sql
-- Add to tables where rows should be deactivatable, not deleted
is_active BOOLEAN NOT NULL DEFAULT true,

-- Filter in all queries
WHERE is_active = true
```

### Timestamps

```sql
-- Add to every table
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()

-- Trigger to keep updated_at current
CREATE TRIGGER set_updated_at
  BEFORE UPDATE ON <table>
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

## What NOT to Do

```
❌ Deferred RLS — policies go in the same migration as the table
❌ Direct UPDATE to fields that should be updated via trigger (e.g. computed totals)
❌ Sequential inserts for multi-table operations — use a transaction/RPC
❌ Hand-editing generated type files — regenerate them
❌ Dropping columns in a migration that application code still reads
```
