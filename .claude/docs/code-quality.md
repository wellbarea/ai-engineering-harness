# Code Quality Rules

> **Template** — document the code-level conventions your team follows.
> These rules are enforced by the Reviewer Agent and should be consistent
> across every module in the codebase.

## Server Actions

```typescript
// ✅ Pattern every Server Action must follow
"use server"

import { z } from "zod"
import { getCurrentUser } from "@/lib/auth"   // or your auth helper
import { revalidatePath } from "next/cache"

const schema = z.object({
  // ... fields
})

export async function myAction(prevState: unknown, formData: FormData) {
  // 1. Authenticate first, before touching anything
  const user = await getCurrentUser()
  if (!user) return { error: "Unauthorized" }

  // 2. Validate input with safeParse — never .parse()
  const result = schema.safeParse(Object.fromEntries(formData))
  if (!result.success) return { error: result.error.flatten().fieldErrors }

  // 3. Business logic / DB call
  const { error } = await db.[operation](result.data)
  if (error) return { error: "Friendly translated error message" }

  // 4. Revalidate after success
  revalidatePath("/relevant-path")
  return { success: true }
}
```

## Data Layer (`lib/data/*.ts`)

```typescript
// ✅ Data functions never throw — return null/[] on error
export async function getThings(): Promise<Thing[]> {
  const { data, error } = await db.from("things").select("*")
  if (error) return []    // ← never throw, never return error objects
  return data
}
```

## Naming Conventions

| Thing | Convention | Example |
|-------|-----------|---------|
| Server Actions | `verb + Noun` | `createProduct`, `updateOrder` |
| Data functions | `get + Noun` | `getProducts`, `getOrderById` |
| Components | PascalCase | `ProductSheet`, `OrderTable` |
| Files | kebab-case | `product-sheet.tsx`, `order-table.tsx` |
| [Add yours] | [convention] | [example] |

## Forms

- Simple forms (≤3 fields, no per-field validation): `useActionState` + `FormData`
- Complex forms (4+ fields, per-field validation): `react-hook-form` + `zodResolver`

## Error Messages

- Never expose raw database error messages to users
- Use a toast library (e.g. sonner) for user-facing feedback
- Server Actions return `{ error: string }` for user-visible errors
- Log internal errors server-side; show a generic message to the client

## Component Patterns

> Document the patterns your project uses for common UI elements.

```
[YOUR PROJECT PATTERNS]

Example:
- List + create/edit → <EntityTable /> + <EntitySheet />
  (Sheet = sliding panel for create/edit forms)
- Confirmation prompts → <AlertDialog /> from the component library
- Loading state → isPending (mutations) / isFetching (queries)
- Empty state → "No [entities] found." (consistent message format)
```

## What NOT to do

```
❌ zod.parse() in Server Actions — use safeParse
❌ alert() or window.confirm() — use component library dialogs
❌ Duplicating a create/edit component — find and extend the existing one
❌ Hardcoded strings for user-visible errors
❌ [Add your project-specific anti-patterns]
```
