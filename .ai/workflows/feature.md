# Feature Workflow

## Objective

Implement a new product feature with traceability and quality validation,
per `.ai/constitution.md` and `.ai/agents/supervisor.md`.

---

# Workflow

## 0. Open a Run

Before intake, open `.ai/logs/runs/run_NNNN/` via the `new-run` skill.
Every step below is written into that run's `plan.md` as it's decided,
not reconstructed afterwards.

---

## 1. Intake

Receive the requested feature. Determine:

- objective
- problem
- scope
- stakeholders (which user roles from `.ai/context/project.md` are affected)
- expected outcome

Check first whether the feature is really new or actually an extension of
an existing module. Most "new features" are additions to existing modules,
not new routes or services.

---

## 2. Product

Product Agent (or the Supervisor when no separate product agent is invoked)
creates:

- product requirements
- user stories
- acceptance criteria
- edge cases

Ground this in `.ai/context/project.md` and `.claude/docs/business-rules.md`.
A feature that "sounds like" a new domain rule may already be covered by an
existing invariant.

Output: `.ai/specs/<feature>/product.md`

---

## 3. Architecture

Architect Agent evaluates, against `.ai/context/architecture.md`:

- which layers are affected
- where new code lives (point to the closest existing module as the reference)
- whether a database migration is needed
- whether a new dependency is needed (must be justified)
- whether the operation needs a database function for atomicity
- technical risks (see Regression section of `.ai/context/quality.md`)

Output: `.ai/specs/<feature>/architecture.md`

---

## 4. Design

Designer Agent (or the implementing agent using `.ai/context/architecture.md`
as the checklist):

- identifies which existing UI pattern to reuse
- specifies all states: loading, empty, error, success
- notes any responsive or accessibility requirements

Output: `.ai/specs/<feature>/design.md` (may be brief if an existing
pattern is reused directly)

---

## 5. Implementation

Implementation agents modify the code:

- **frontend** — UI changes
- **backend** — database, migrations, access control, functions

Each agent must:

- follow project conventions (`.claude/docs/code-quality.md`)
- keep scope minimal (no unrelated cleanup of touched files)
- report all changed files

---

## 6. Validation

Run quality gates (skill `check`):

- lint
- typecheck
- build
- tests (if configured)

If the feature touched the database, confirm the migration applies cleanly
and generated types were regenerated.

---

## 7. QA

QA Agent validates, per `.ai/context/quality.md`'s QA Principles:

- acceptance criteria (from `.ai/specs/<feature>/product.md`)
- happy path
- edge cases documented in the spec
- domain edge cases from `.claude/docs/business-rules.md`
- error states (friendly message, not raw error)
- loading and empty states
- regression checklist (`.ai/context/quality.md` → Regression)

---

## 8. Review

Reviewer Agent checks against `.ai/constitution.md` and
`.claude/docs/code-quality.md`:

- requirements match spec
- architecture matches spec
- implementation follows conventions
- scope is clean (no unrelated changes)
- regressions in the touched area

---

## 9. Evidence

Generate:

- change log → `.ai/logs/changes/<feature>.md`
- QA report → `.ai/logs/qa/<feature>.md`
- final execution log → `.ai/logs/runs/run_NNNN/run-log.json`
  (run id, task, agents, files changed, gates, failures, corrections, final status)
- update `.ai/logs/runs/INDEX.md` to close the run

If the feature required a non-obvious architectural call, record it in
`.ai/decisions/`. If a failure surfaced a lesson for future runs, record
it in `.ai/memory/`.

---

## 10. Completion

Feature may only be considered complete when:

- quality gates are all green
- QA walkthrough done
- domain invariants respected
- evidence recorded per step 9

Agent claims alone are never sufficient
(`.ai/constitution.md`, Principles 3 and 10).
