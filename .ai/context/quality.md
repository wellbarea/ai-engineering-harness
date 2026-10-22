# Quality Engineering Rules

> **Template** — replace every `[PLACEHOLDER]` with your project's real values.

## Quality Objectives

The application must prioritize, in this order:

1. **Correctness** — [describe what incorrect means in your domain, e.g. "financial calculations, inventory counts, and access control must be exact"]
2. **Reliability** — [describe atomicity/consistency requirements, e.g. "multi-step operations must be atomic — no partial state"]
3. **Security** — [describe isolation requirements, e.g. "tenant isolation enforced at DB level, not application layer"]
4. **Maintainability** — every module must follow the established patterns so the codebase reads consistently
5. **Accessibility** — inherited from the component library; not optional
6. **Performance** — [describe key performance constraints, e.g. "server-side pagination, no full table scans on the client"]

---

## Required Quality Gates

### Static Analysis

> Replace with your project's actual commands.

```bash
[your-package-manager] lint        # e.g. pnpm lint
[your-package-manager] typecheck   # e.g. pnpm typecheck  (tsc --noEmit, strict: true)
[your-package-manager] build       # e.g. pnpm build
```

All three must pass before any change is considered done. Run together
via the `check` skill.

### Tests

> Describe what automated testing exists (or doesn't).

**[Configure this for your project]**

Options:
- Unit tests: [configured / not configured — if not, note it explicitly]
- Integration tests: [configured / not configured]
- E2E / browser tests: [configured — e.g. Playwright at `tests/e2e/`]

If no test framework is configured, note it here explicitly and do not
silently introduce one inside a feature task — align with the team first.

### UI Validation

For any UI change, validate:

- Loading state (async operations show feedback)
- Empty state (empty results show a meaningful message, not a blank screen)
- Error state (friendly error message, not a raw internal error)
- Responsive behavior (at relevant breakpoints for your users)
- Accessibility (keyboard navigation and focus on interactive elements)

---

## Regression Areas

> List the areas of your codebase where bugs have the highest impact.
> Agents check these before every change.

### [High-Risk Area 1: e.g. Financial Calculations]

[Describe the invariant that must hold, e.g. "Account balance is always
computed from the transaction ledger, never stored directly"]

### [High-Risk Area 2: e.g. Access Control]

[Describe the invariant, e.g. "Every table has row-level security policies;
no query may bypass them"]

### [High-Risk Area 3: e.g. Inventory / Stock]

[Describe the invariant, e.g. "Stock level is updated only via database
triggers on movement records — never via direct UPDATE"]

---

## QA Principles

QA must attempt to find failures.

QA must not assume that an implementation is correct because the
implementation agent reports success.

Specifically:

- Verify domain invariants directly — don't trust the agent's description
  of what happened
- Try the error path, not just the happy path
- Check that error messages shown to users are friendly, not raw internals
- For any domain-specific edge case documented in
  `.claude/docs/business-rules.md`, verify it explicitly

---

## Severity

### Critical

Blocks release. Examples: data leaking across tenant boundaries, incorrect
financial calculations, broken authentication, incorrect access control.

### High

Should be fixed before approval. Examples: missing authentication check in
a mutation, multi-table write not in a transaction, raw error message
exposed to users, regression in a critical flow.

### Medium

Should normally be fixed before completion. Examples: inconsistency with
established layer pattern, missing loading/empty state, unintentional lint
warning, paginated list missing server-side pagination.

### Low

Can be documented for future work. Examples: naming slightly off-pattern,
missed reuse opportunity, missing automated test for a new code path.
