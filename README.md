# AI Engineering Harness

A structured framework for shipping software with AI agents — not just generating code, but **planning, implementing, validating and auditing** every change.

Built and battle-tested across 80+ feature runs on a production SaaS, now extracted as a reusable template for any project.

---

## The Problem

Using Claude Code (or any AI agent) naively produces a common failure mode:

- Agent writes code → looks correct → merged → breaks in production
- No record of what was decided or why
- Quality gates skipped because "the agent said it passed"
- Architecture drifts because each session starts from scratch
- When something fails, there's no trace of what happened

The Harness addresses all of this.

---

## What This Is

An opinionated folder structure and set of Markdown files that wire Claude Code into a **disciplined engineering workflow**:

```
.ai/                        ← Harness brain
  constitution.md           ← Core principles, non-negotiable rules
  agents/supervisor.md      ← Orchestrator: plans, delegates, validates
  agents/{product,architect,designer,frontend,backend,qa,reviewer}.md
  context/                  ← Living project docs agents read before acting
    project.md              ← Business domain, users, stack
    architecture.md         ← Layer boundaries, forbidden patterns
    quality.md              ← Quality gates, regression areas
  workflows/feature.md      ← End-to-end feature delivery workflow
  specs/                    ← Per-feature specs (product + architecture + design)
  logs/
    runs/                   ← Every AI run: plan + audit log (run_0001, run_0002, ...)
    changes/                ← Human-readable change history
    qa/                     ← QA reports
    failures/               ← Failure records → become rules
  decisions/                ← Architectural decisions that outlive any run
  memory/                   ← Lessons that should carry into future sessions
  templates/run-log.json    ← Schema for audit logs

.claude/                    ← Claude Code configuration
  agents/                   ← Subagent definitions (invocable via Agent tool)
  docs/                     ← Project-specific context documents
  skills/                   ← Custom slash commands (new-run, check, ...)
```

---

## Core Principles (from `.ai/constitution.md`)

1. **Specification Before Implementation** — no agent writes code without a defined spec
2. **Agents Have Explicit Responsibilities** — roles don't overlap
3. **Evidence Over Claims** — "I believe it works" is never sufficient
4. **Quality Gates Are Mandatory** — lint + typecheck + build before done
5. **Changes Must Be Traceable** — every run gets a numbered folder with plan + log
6. **Failures Are Learning Opportunities** — failures become rules
7. **Preserve Existing Behavior** — regression risk is assessed before every change
8. **Minimal Scope** — agents modify only what's needed
9. **Human Approval** — plan presented and approved before execution
10. **Never Hide Failures** — unsuccessful work is never reported as success

---

## How a Feature Gets Built

```
new-run skill
    │
    ▼
Plan written in .ai/logs/runs/run_NNNN/plan.md
    │
    ▼ (human approves)
    │
    ├─ Product agent  →  .ai/specs/<feature>/product.md
    ├─ Architect agent →  .ai/specs/<feature>/architecture.md
    ├─ Designer agent  →  .ai/specs/<feature>/design.md
    ├─ Frontend agent  →  implementation
    ├─ Backend agent   →  migrations / RPCs
    ├─ QA agent        →  .ai/logs/qa/<feature>.md
    └─ Reviewer agent  →  final sign-off
         │
         ▼
    Quality gates pass (lint + typecheck + build)
         │
         ▼
    .ai/logs/runs/run_NNNN/run-log.json  ←  audit record
    .ai/logs/changes/<feature>.md        ←  change log
    .ai/logs/runs/INDEX.md updated       ←  ledger
```

---

## The Audit Trail

Every AI-driven task opens a numbered run **before** any code is touched:

```
.ai/logs/runs/
  INDEX.md              ← ledger of all runs, their status and outcomes
  run_0001/
    plan.md             ← what was planned, written BEFORE execution
    run-log.json        ← audit record: agents, files, gates, failures, commit
  run_0002/
    plan.md
    run-log.json
  ...
```

This creates an auditable history of every decision, every failure, and every correction — the kind of transparency that makes AI-generated code safe to ship to production.

---

## Getting Started

### 1. Copy the harness into your project

```bash
cp -r .ai your-project/
cp -r .claude your-project/
cp CLAUDE.md your-project/
```

### 2. Fill in the context files

These are the "project brain" — agents read them before acting:

| File | What to put there |
|------|-------------------|
| `.ai/context/project.md` | Your product, users, stack, constraints |
| `.ai/context/architecture.md` | Layer boundaries, module map, forbidden patterns |
| `.ai/context/quality.md` | Your quality gates, regression risk areas |
| `.claude/docs/` | Deeper docs: DB conventions, code quality rules, project structure |

### 3. Update the CLAUDE.md

Point `CLAUDE.md` at your filled context files. This is what Claude reads on every session start.

### 4. Customize the agents

The agent files in `.claude/agents/` and `.ai/agents/` reference your specific project. Do a find-and-replace on `[YOUR PROJECT]` placeholders.

### 5. Open your first run

```
/new-run
```

The `new-run` skill guides you through opening a numbered run folder, writing a plan, and getting human approval before touching any code.

---

## What Makes This Different

| Approach | What happens |
|----------|-------------|
| Naive AI coding | Generate → merge → hope |
| This harness | Plan → approve → implement → gate → validate → log |

The Harness doesn't make AI slower — it makes AI output **trustworthy enough to ship**.

Key properties:
- **Plan-first**: every run has a written plan before execution starts
- **Gated**: quality checks are mandatory, not optional
- **Auditable**: full trace of every run, decision, and failure
- **Accumulative**: failures become rules; rules improve future runs
- **Human-in-the-loop**: approval before execution, not after

---

## Real-World Example

To illustrate what this looks like in practice, here's a fictional project —
**Storefront** (a multi-tenant SaaS for retail stores built with Next.js + Supabase).

**Run index after ~80 features shipped:**

```
.ai/logs/runs/INDEX.md

| Run      | Date       | Task                              | Status | Spec                    |
|----------|------------|-----------------------------------|--------|-------------------------|
| run_0001 | 2025-01-10 | product catalog CRUD              | done   | specs/catalog/          |
| run_0002 | 2025-01-12 | inventory movement triggers       | done   | specs/inventory/        |
| run_0015 | 2025-02-03 | Stripe subscription billing       | done   | specs/billing/          |
| run_0031 | 2025-03-18 | point-of-sale barcode scanner     | done   | specs/pos/              |
| run_0047 | 2025-04-22 | installment payment module        | done   | specs/installments/     |
| run_0062 | 2025-05-30 | password recovery flow            | done   | -                       |
| run_0080 | 2025-06-14 | bulk product import via CSV       | done   | specs/product-import/   |
```

**A decision that outlived its run:**

```markdown
# .ai/decisions/2025-03-01-inventory-update-via-trigger.md

Decision: inventory quantity is updated only via a Postgres trigger on the
movements table — never via a direct UPDATE to the products table.

Reason: two concurrent sales were tested and produced negative stock when
using application-level sequential updates. The trigger + SELECT FOR UPDATE
lock eliminates the race condition at the database level.

Impact: any feature touching inventory must go through the movements table,
not products directly. Architect agent checks this on every inventory-adjacent run.
```

**A spec that kept QA honest:**

```markdown
# .ai/specs/pos/product.md — Acceptance Criteria (excerpt)

- [ ] Scanning an out-of-stock barcode shows "Insufficient stock" toast, does not add to cart
- [ ] Products with `track_inventory: false` can always be added regardless of stock level
- [ ] Completing a sale with an empty cart is blocked at the action layer, not only in UI
- [ ] Session expiry mid-sale redirects to login without losing the cart (localStorage)
```

**Outcome after 80 runs:**
- Zero production incidents caused by a quality gate being skipped
- Every non-obvious architectural call has a written rationale in `.ai/decisions/`
- Failures from early runs (e.g. the inventory race condition above) became
  permanent rules checked on every subsequent run touching that area

---

## Stack Compatibility

The harness is **stack-agnostic**. The principles and workflow work for any tech stack. The template context files have placeholders (`[YOUR STACK]`, `[YOUR FRAMEWORK]`) for you to fill in.

The example skills use `pnpm` — swap those for your package manager / build tool.

---

## License

MIT — use it, adapt it, share it.

---

*Built while shipping a real product with Claude Code.*
