# Business Rules

> **Template** — document the domain invariants that must never be violated.
> These are the rules that, if broken, corrupt data or break core user trust.
>
> Agents read this before implementing anything that touches these domains.
> A request that "sounds like" a new rule is often already covered here — check first.

## What belongs here

Domain invariants: rules about *what the system must always guarantee*, not
implementation details. These are facts about your business domain that are
enforced by the database, the application, or both.

Examples of what to document:
- "Only one [X] can be active at a time per [Y]"
- "Quantity can never go below zero"
- "[Multi-step operation] must be atomic — partial state is invalid"
- "[Entity A] can only be modified by [Role] when in [State]"

## [Domain Area 1: e.g. Inventory]

**[Invariant 1]**
[Description of the rule and why it exists]

Implementation: [where this is enforced — DB trigger / RPC / application layer]

**[Invariant 2]**
[Description]

---

## [Domain Area 2: e.g. Financial]

**[Invariant 1]**
[Description]

---

## [Domain Area 3: e.g. Access Control / Multi-tenancy]

**[Invariant 1]**
[Description of tenant isolation rule]

---

## Edge Cases That Repeat

> List the edge cases that repeatedly catch agents off guard in this domain.
> Each one should become a QA step whenever the adjacent area is touched.

- **[Edge case 1]**: [description — e.g. "A product with `track_inventory: false` must never appear in low-stock queries"]
- **[Edge case 2]**: [description]
- **[Edge case 3]**: [description]
