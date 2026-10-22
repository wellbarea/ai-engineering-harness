# Project Structure

> **Template** — map your project's directories and explain where new code goes.
> Agents read this to decide placement for new files.

## Directory Map

```
[YOUR PROJECT ROOT]/
├── src/
│   ├── app/
│   │   ├── (app)/                 ← authenticated routes
│   │   │   ├── [module-1]/
│   │   │   │   ├── page.tsx       ← Server Component, composition only
│   │   │   │   ├── actions.ts     ← Server Actions (writes)
│   │   │   │   └── [components]   ← module-local Client Components
│   │   │   ├── [module-2]/
│   │   │   └── layout.tsx         ← authenticated shell
│   │   ├── (public)/              ← unauthenticated routes (marketing, login)
│   │   └── api/                   ← Route Handlers (webhooks, etc.)
│   ├── components/                ← shared UI components
│   ├── lib/
│   │   └── data/                  ← server-only, read-only DB queries
│   ├── hooks/                     ← shared React hooks (used in 2+ places)
│   ├── config/                    ← shared constants/config
│   └── types/                     ← TypeScript type definitions
├── [YOUR DB MIGRATIONS DIR]/      ← e.g. supabase/migrations/
└── tests/                         ← automated tests
```

## Where to Put New Code

| What | Where |
|------|-------|
| New feature (new route) | `src/app/(app)/<feature>/` |
| New DB query | `src/lib/data/<module>.ts` |
| New Server Action | `src/app/(app)/<module>/actions.ts` |
| New shared component | `src/components/` |
| New hook used in 2+ places | `src/hooks/` |
| New config constant used in 2+ places | `src/config/<topic>.ts` |
| New DB migration | `[migrations dir]/` via the `db-migration` skill |

## What NOT to Do

- Do not put a read query in `actions.ts` — it belongs in `lib/data/`
- Do not put business logic in `page.tsx` — it belongs in `actions.ts`
- Do not duplicate a shared component — find it in `src/components/`
- Do not put a constant used in only one file in `src/config/` — keep it local
- Do not create a new `hooks/` file for something only one component uses

## Module Structure

Every feature module under `src/app/(app)/` follows the same structure:

```
<module>/
├── page.tsx          ← Server Component
│                        Reads initial data with lib/data/<module>.ts
│                        Passes data to Client Components
│                        No business logic
│
├── actions.ts        ← Server Actions
│                        Validates input (zod.safeParse)
│                        Checks authentication
│                        Performs writes
│                        Calls revalidatePath
│
└── [component].tsx   ← Client Components (only what needs interactivity)
                         Sheet/Dialog for create/edit forms
                         Table for listing
```
