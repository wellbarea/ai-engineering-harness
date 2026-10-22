---
name: check
description: Run lint, typecheck and build, then summarize errors. Use before considering any code change done, or when asked to "verify", "validate" or "run the checks".
---

# Run quality gates

This skill runs the project's mandatory quality gates and reports any errors.

> **Customize the commands below for your project's tooling.**

```bash
# Run all gates
[package-manager] lint        # e.g. pnpm lint  /  npm run lint
[package-manager] typecheck   # e.g. pnpm typecheck
[package-manager] build       # e.g. pnpm build  /  npm run build
```

All three must pass before any change is considered done
(`.ai/constitution.md` Principle 4).

## What to report

- **Lint**: number of errors and warnings; paste any error lines
- **Typecheck**: number of type errors; paste any error lines
- **Build**: pass or fail; paste the first error if it fails
- **Overall**: PASS (all three green) or FAIL (list which failed)

## If a gate fails

1. Read the error output carefully before attempting a fix
2. Fix the root cause, not a symptom (do not add `// @ts-ignore`)
3. Re-run the failing gate to confirm the fix
4. If a gate was already failing before your change (pre-existing), note
   that clearly rather than claiming the failure is caused by your change

## Notes

- TypeScript `strict: true` is the baseline — do not disable strict mode
  or individual strict checks to make errors go away
- Lint warnings in test or generated files are generally expected — confirm
  with `.ai/context/quality.md` which warnings are known/accepted
