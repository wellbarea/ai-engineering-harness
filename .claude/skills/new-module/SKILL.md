---
name: new-module
description: Checklist for creating a new feature module/route following the established data/actions/page pattern. Use when asked to build a new screen or CRUD module.
---

# New Module Checklist

Use this when creating a new feature module — a new route with a list,
create/edit forms, and actions.

Follow the established pattern exactly. The closest existing module in
`src/app/(app)/` is your reference — read it before starting.

## Pre-flight

- [ ] Open a run via `new-run` skill first
- [ ] Confirm the feature has an approved spec in `.ai/specs/<feature>/`
- [ ] Identify the closest existing module to use as reference
- [ ] Confirm with `.claude/docs/project-structure.md` where files go

## Database (if needed)

- [ ] Migration created via the `db-migration` workflow
- [ ] Access control policies in the **same** migration
- [ ] Generated types regenerated after migration applies

## Data layer (`lib/data/<module>.ts`)

- [ ] `server-only` import at the top
- [ ] All functions return `Type[] | null` — never throw
- [ ] Uses the server Supabase/DB client
- [ ] No writes in this file

## Actions (`app/(app)/<module>/actions.ts`)

- [ ] `"use server"` directive at top
- [ ] Every action checks authentication first
- [ ] Input validated with `zod.safeParse` (never `.parse()`)
- [ ] Returns `{ error: string }` on failure (friendly message, not raw DB error)
- [ ] Calls `revalidatePath` after successful mutations
- [ ] No reads that belong in the data layer

## Page (`app/(app)/<module>/page.tsx`)

- [ ] Server Component
- [ ] Reads initial data via `lib/data/<module>.ts`
- [ ] No business logic — only composition
- [ ] Passes data to Client Components as props

## UI Components

- [ ] Reuses an existing pattern (Sheet/Dialog/Table — point to the reference)
- [ ] Loading state implemented (`isPending` on form submit, `isFetching` on list)
- [ ] Empty state implemented (`"No [entities] found."`)
- [ ] Error state via toast (not raw error message)
- [ ] Keyboard accessible (inherits from component library primitives)

## Quality gates

- [ ] `check` skill: lint + typecheck + build all pass
- [ ] Manual validation: happy path, empty state, error state
- [ ] QA report written in `.ai/logs/qa/<feature>.md`
