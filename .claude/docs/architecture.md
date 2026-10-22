# Architecture

> **Template** — fill in the concrete details of your project's architecture.
> This file is what agents read to understand *how* to build, not *what* to build.

## Layer Overview

Describe your project's architectural layers here. Be specific about:

- What each layer is responsible for
- What it is NOT responsible for
- How data flows between layers
- What the entry/exit contracts are (return types, error handling)

```
[YOUR LAYER DIAGRAM]

Example for a Next.js + Supabase project:

lib/data/<module>.ts          ← server-only, read-only
    Fetches from DB; returns null/[] on error; never throws.
    Imported only by Server Components and Server Actions.

app/(app)/<module>/actions.ts ← server-only, writes
    Validates input with zod.safeParse.
    Checks getCurrentUser() before every mutation.
    Returns { error: string } on failure (never throws to client).
    Calls revalidatePath after successful mutations.

app/(app)/<module>/page.tsx   ← Server Component, composition only
    Reads initial data by calling lib/data directly.
    Passes data to Client Components as props.
    No business logic.

src/components/               ← shared presentational components
    Client Components only.
    No direct DB access.
```

## Directory Map

| Directory | Purpose |
|-----------|---------|
| `src/app/(app)/` | Authenticated feature routes |
| `src/app/api/` | Route handlers (webhooks, etc.) |
| `src/components/` | Shared UI components |
| `src/lib/data/` | Read-only server data access |
| `src/hooks/` | Shared React hooks (2+ usages) |
| `src/config/` | Shared constants/config |
| `src/types/` | TypeScript types |
| `[add yours]` | [purpose] |

## Supabase Clients

> If using Supabase, document which client to use where.

- `createServerClient` (from `@supabase/ssr`) — Server Components, Server Actions, Route Handlers
- `createBrowserClient` (from `@supabase/ssr`) — Client Components that need reactive subscriptions
- Never use the admin/service-role client in user-facing code

## Authentication Pattern

> Document how authentication is checked and enforced.

```typescript
// Example pattern — adapt to your project
const user = await getCurrentUser()
if (!user) return { error: "Unauthorized" }
```

## Forbidden Patterns

> These are explicitly prohibited. If you need to do one of these, surface
> the question to the human instead of proceeding.

```
❌ Importing server-only files into Client Components
❌ Writing to the database from the data layer (read-only)
❌ Business logic in page.tsx
❌ zod.parse() instead of zod.safeParse() in Server Actions
❌ Hand-editing generated type files
❌ Multi-table mutations without a transaction/database function
❌ Exposing raw database error messages to users
❌ [Add your project-specific forbidden patterns]
```
