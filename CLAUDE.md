@AGENTS.md

# AI Engineering Harness

This project uses an AI Engineering Harness under `.ai/`.
Do not treat AI-generated code as automatically correct — every
meaningful change must be understood, planned, implemented, validated,
reviewed and recorded.

- Rules: `.ai/constitution.md`
- Context: `.ai/context/` (`project.md`, `architecture.md`, `quality.md`) — load only what's relevant to the task
- Orchestration: `.ai/agents/supervisor.md`
- Subagents: `.claude/agents/` — `product`, `architect`, `designer`, `frontend`, `backend`, `qa`, `reviewer`
- Workflows: `.ai/workflows/`
- Specs: `.ai/specs/` (template in `.ai/specs/_template/`)
- Decisions: `.ai/decisions/`
- Audit logs: `.ai/logs/` (schema: `.ai/templates/run-log.json`)
- Memory: `.ai/memory/`

Never claim a task is complete based on the implementation alone —
verify against the quality gates in `.ai/context/quality.md` first. Keep
changes scoped to the task; report discovered extra work instead of
silently expanding it. The human remains the final authority on
significant architectural, business and production decisions.
