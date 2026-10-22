# Architecture Context

> **Template** — replace every `[PLACEHOLDER]` with your project's real values.
> This file is the authoritative reference for layer boundaries. Agents read
> it before deciding where new code goes.

## Layer Model

```
[YOUR LAYER DIAGRAM]

Example for a Next.js + Supabase project:

lib/data/<module>.ts      ← read-only server code ("server-only" import)
    │  returns null/[] on error, never throws
    ▼
app/(app)/<module>/actions.ts  ← writes only, Server Actions
    │  validates input with zod.safeParse
    │  checks authentication before any DB call
    │  calls revalidatePath after mutations
    ▼
app/(app)/<module>/page.tsx    ← composition, no business logic
    │  Server Component reads initial data
    │  Client Components receive data as props
    ▼
src/components/               ← shared presentational components
```

## Module Map

> List each top-level module and what it owns.

| Module | Path | Owns |
|--------|------|------|
| [module-1] | `src/app/(app)/[module-1]/` | [what domain concept] |
| [module-2] | `src/app/(app)/[module-2]/` | [what domain concept] |

## Shared Code Placement

- Reusable UI components → `src/components/`
- Reusable hooks → `src/hooks/` (when used in 2+ places)
- Shared config/constants → `src/config/<topic>.ts`
- Type definitions → `src/types/`

## Forbidden Patterns

> These are explicitly prohibited. If you find yourself needing one of these,
> surface the question to the human instead of doing it.

- [ ] Importing `server-only` data files from Client Components
- [ ] Direct database writes from the data layer (read-only)
- [ ] Business logic in `page.tsx` (belongs in `actions.ts` or an RPC)
- [ ] `zod.parse()` instead of `zod.safeParse()` in Server Actions
- [ ] Hand-editing generated type files (e.g. database type definitions)
- [ ] Multi-table mutations as sequential statements without a transaction
- [ ] [Add your project-specific forbidden patterns here]

## High-Risk Areas

> Areas where bugs have severe consequences. Extra care required.

- **[Area 1]**: [Why it's risky, what invariants must hold]
- **[Area 2]**: [Why it's risky, what invariants must hold]
- **[Area 3]**: [Why it's risky, what invariants must hold]

## Decision Log

Significant architectural decisions are in `.ai/decisions/`. Read the
relevant ones before proposing a change that might conflict.
