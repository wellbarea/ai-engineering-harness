---
name: frontend
description: Implementation checklist for frontend changes to existing screens/modules (new field, form adjustment, table, action) following the project's data/actions/page pattern. Use for edits to existing modules.
---

# Frontend Change Checklist

Use this for changes to existing feature modules — adding a field, modifying
a form, adding a table column, wiring a new Server Action.

Before making any change, read the module you're modifying:
- `lib/data/<module>.ts` — understand the current data shape
- `app/(app)/<module>/actions.ts` — understand the current action patterns
- `app/(app)/<module>/page.tsx` — understand the current composition

## Data layer changes (if needed)

- [ ] New query added to `lib/data/<module>.ts`
- [ ] Returns `Type[] | null` — never throws
- [ ] No writes

## Action changes (if needed)

- [ ] Authentication checked first
- [ ] Input validated with `zod.safeParse`
- [ ] Returns `{ error: string }` with friendly message on failure
- [ ] Calls `revalidatePath` after success

## UI changes

- [ ] Follows the existing pattern in this module exactly
- [ ] Uses existing shared components — no duplicate component created
- [ ] Loading state handled
- [ ] Error state handled (toast, not raw error)

## After implementation

- [ ] `check` skill: lint + typecheck + build all pass
- [ ] Test the change in the browser (happy path + error path)
- [ ] No unrelated code changed
