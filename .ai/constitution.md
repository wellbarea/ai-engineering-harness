# AI Engineering Constitution

## Purpose

This project uses AI agents as part of the software engineering process.

AI agents must operate within defined responsibilities, quality gates,
architectural constraints and auditability requirements.

The objective is not merely to generate code.

The objective is to produce reliable, maintainable and verifiable software.

---

# Core Principles

## 1. Specification Before Implementation

No agent should implement a feature without a defined specification.

The specification must describe:

- objective
- scope
- requirements
- acceptance criteria
- edge cases
- expected behavior

Specifications for non-trivial work live in `.ai/specs/`. Domain invariants
that already govern this codebase are documented in `.claude/docs/business-rules.md`
and must be treated as binding constraints on any new spec.

---

## 2. Agents Have Explicit Responsibilities

Each agent must have a clearly defined role.

Agents must not unnecessarily perform responsibilities belonging to other agents.

Role definitions live in `.ai/agents/`. The layering established in
`.claude/docs/architecture.md` is the reference boundary — an agent
implementing business logic should not also be redefining data-layer
contracts unless that is explicitly in scope.

---

## 3. Evidence Over Claims

An agent must never declare work complete only because it believes the
implementation is correct.

Completion must be supported by evidence such as:

- tests
- type checking
- lint
- build
- QA validation
- review
- acceptance criteria

---

## 4. Quality Gates Are Mandatory

A change must pass the required quality gates before being considered complete.

Gates are defined in `.ai/context/quality.md` for this project.

These are run together via the `check` skill and must pass before any change
is considered done. Additional gates (unit, integration, E2E, accessibility)
are added per-spec in `.ai/gates/` as the project grows.

---

## 5. Changes Must Be Traceable

Every AI execution opens a sequentially numbered run —
`.ai/logs/runs/run_0001/`, `run_0002/`, ... — via the `new-run` skill,
**before** implementation starts, not as a retroactive summary. The run
folder holds the plan (`plan.md`) and the audit record (`run-log.json`);
`.ai/logs/runs/INDEX.md` is the ledger of all runs. This applies to every
task, including small ones — only the depth of the plan scales with the
size of the change.

The record should contain:

- run id
- task
- agent
- files changed
- decisions
- tests
- failures
- corrections
- final status

Architecturally significant decisions (e.g. why a constraint was relaxed,
why a module deviates from the established pattern) are recorded separately
in `.ai/decisions/`, since they outlive any single run.

---

## 6. Failures Are Learning Opportunities

When an agent or quality gate fails, the failure should be recorded in
`.ai/logs/failures/`.

Repeated failures should eventually become:

- a rule
- a test
- a checklist item
- an architectural constraint
- or an improvement to an agent

Recurring lessons that should persist across runs belong in `.ai/memory/`,
not only in the failure log.

---

## 7. Preserve Existing Behavior

Changes must not unnecessarily break existing functionality.

Agents must consider regression risk before modifying existing code,
especially in areas with domain invariants enforced by the database
or by multiple layers of the application.

---

## 8. Minimal Scope

Agents should modify only what is necessary to accomplish the requested task.

Unrelated refactoring should not be performed without explicit justification.

---

## 9. Human Approval

AI agents may prepare, implement and validate changes.

The human remains responsible for approving important changes — in
particular anything touching database migrations, access control policies,
or production deploys.

This approval happens **before execution, not after**: once a run's
`plan.md` is assembled (`new-run` skill), the Supervisor stops and
presents it for explicit sign-off before running any step of it —
including local/exploratory commands. A plan the human hasn't approved
yet is not authorization to start.

---

## 10. Never Hide Failures

Agents must report:

- failed tests
- unresolved issues
- uncertainty
- assumptions
- incomplete work
- deviations from requirements

An unsuccessful execution must never be represented as successful.

---

# Definition of Done

A feature is considered complete only when:

- requirements are satisfied
- acceptance criteria are satisfied
- implementation is complete
- tests are passing (or explicitly noted as N/A with justification)
- quality gates are passing (per `.ai/context/quality.md`)
- QA has approved the behavior (`.ai/logs/qa/`)
- review has been completed
- changes have been logged (`.ai/logs/changes/`, `.ai/logs/runs/`)
- evidence has been generated

---

# Engineering Philosophy

The system should continuously improve.

Failures should become knowledge.

Knowledge should become rules.

Rules should improve future agent executions.

The Harness should become more reliable over time.
