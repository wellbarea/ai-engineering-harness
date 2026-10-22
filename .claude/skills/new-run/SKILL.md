---
name: new-run
description: Open a new numbered run (run_0001, run_0002, ...) in `.ai/logs/runs/` with the plan written before any execution. Use at the start of every engineering task — before implementing, investigating or running a migration — to ensure sequential numbering and a recorded plan before execution begins.
---

# Open a new run

Every engineering task in this project starts here, per
`.ai/constitution.md` (Principle 5, Traceability) and
`.ai/agents/supervisor.md`. Do not skip this step even for a small change
— what scales with task size is the *depth* of the plan, not whether a run
exists.

1. Find the next run number:
   ```bash
   ls -1 .ai/logs/runs | grep -E '^run_[0-9]{4}$' | sort | tail -1
   ```
   Add 1, zero-padded to 4 digits (or start at `run_0001` if none exist yet).

2. Create the run folder:
   ```bash
   mkdir -p .ai/logs/runs/run_NNNN
   ```

3. Write the plan in `.ai/logs/runs/run_NNNN/plan.md` **before touching any
   code**, with these sections:
   - **Task** — what was requested, in one sentence
   - **Workflow** — feature / bug / refactor / technical-debt /
     investigation / documentation / db-migration (see
     `.ai/agents/supervisor.md#plan` for how to choose)
   - **Scope** — in scope / out of scope
   - **Spec** — link to `.ai/specs/<feature>/` if the change is
     non-trivial enough to justify a full spec; otherwise write
     "trivial — full spec not needed" and the reason
   - **Agents** — which subagents from `.claude/agents/` will be called
     and in what order (may be none for a one-liner fix)
   - **Steps** — ordered list of execution steps
   - **Quality gates** — which gates from `.ai/context/quality.md` apply
   - **Human approval** — if the change touches database migrations, access
     control, or production deploys, mark that human approval is required
     (`.ai/constitution.md` Principle 9)

4. Copy `.ai/templates/run-log.json` to
   `.ai/logs/runs/run_NNNN/run-log.json` and fill in immediately:
   `run_id`, `task`, `workflow`, `started_at` (ISO 8601).
   Leave the rest (`files_changed`, `quality_gates`, `failures`, `status`)
   to be filled during/after execution — never set `"status": "done"`
   before the gates pass (`.ai/constitution.md` Principle 3).

5. Add a row to `.ai/logs/runs/INDEX.md`:
   ```
   | run_NNNN | <ISO date> | <short task> | awaiting_approval | <spec or "-"> |
   ```

6. **Stop and present the plan to the human.** Do not execute any step —
   not even exploratory commands — before explicit approval. Once approved,
   update `run-log.json` and `INDEX.md` to `in_progress` and begin. When done:
   - update `run-log.json` (`finished_at`, `status`, `agents`,
     `files_changed`, `quality_gates`, `failures`, `corrections`,
     `human_approval`, `commit`)
   - update the INDEX.md row to `done` / `failed` / `blocked`
   - if the task required a non-obvious architectural decision, record it
     in `.ai/decisions/`; if it surfaced a lesson for future runs, record
     it in `.ai/memory/`

## Golden rule

The plan (`plan.md`) is written **before** implementation, not afterwards
as retroactive documentation. If you've already implemented something and
are only now writing the plan, the process was not followed — record the
run anyway with a note about the inverted order, and open the run first
next time.
