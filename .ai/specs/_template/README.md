# Feature Specification

> Copy this folder to `.ai/specs/<feature>/` and fill in each section,
> following the workflow in [`.ai/workflows/feature.md`](../../workflows/feature.md).
> Do not delete the italic instructions until you've filled the section —
> they point to where the necessary context lives.

## Feature

_Short name and the module(s) affected (see `.ai/context/architecture.md`
for the module map — or "new module" if none applies)._

## Objective

_What this feature delivers, in one sentence._

## Problem

_What real user or business problem motivates this? See the business domain
in `.ai/context/project.md`._

## Scope

### In Scope

### Out of Scope

## User Stories

_Who uses this (which role from `.ai/context/project.md`) and what they
need to do._

## Acceptance Criteria

_Verifiable criteria, not implementation description. Each criterion should
map directly to a QA step in `.ai/logs/qa/<feature>.md`._

## Edge Cases

_Consider the invariants already documented in `.claude/docs/business-rules.md`
before inventing new ones. Common recurring cases: expired session,
unauthorized role, empty/invalid input, concurrent mutation of shared state._

## Technical Considerations

_Which layers are affected (see `.ai/context/architecture.md`), whether a
database migration is needed, whether new dependencies are required (must
be justified), whether atomicity requires a database function._

## Risks

_High-risk areas for this domain — see the Regression section of
`.ai/context/quality.md`. Flag anything that could cause a regression in
existing critical flows._

## Dependencies

_Other specs, migrations, or decisions in `.ai/decisions/` that this
feature depends on._

## Status

- [ ] Product
- [ ] Architecture
- [ ] Design
- [ ] Implementation
- [ ] Tests
- [ ] QA
- [ ] Review
- [ ] Evidence
