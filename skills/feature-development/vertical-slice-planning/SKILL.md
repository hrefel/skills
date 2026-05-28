---
name: vertical-slice-planning
description: Use when decomposing a feature into independently deliverable implementation units. Each slice should be shippable end-to-end and independently verifiable.
---

## Purpose

Decompose a feature into vertical slices — thin, independently deliverable units that each touch all necessary architecture layers. Slices minimize work-in-progress and allow incremental delivery.

---

## What is a Vertical Slice

A vertical slice is a unit of work that:

- Delivers a single, self-contained behavior
- Touches every layer needed to make that behavior work (domain → application → infrastructure → UI if needed)
- Can be independently verified against its acceptance criteria
- Can be shipped without waiting for other slices to complete

A vertical slice is **not**:
- A horizontal layer ("implement all domain entities first")
- A technical task ("add the database table")
- An epic-sized chunk of work

---

## Process

### Step 1: Identify Independent Behaviors

From the clarified requirements and acceptance criteria, list every distinct user-facing behavior. Each behavior is a candidate slice.

Ask for each candidate:
- Can this behavior be verified without the others?
- Does it deliver standalone value?
- Can it be implemented without another incomplete slice?

### Step 2: Identify Dependencies

Map which slices depend on others. A slice B depends on slice A if:
- B requires data or state that A creates
- B builds on a domain entity or port that A introduces
- B's acceptance criteria assume A is already working

### Step 3: Sequence by Dependency

Order slices so that no slice begins before its dependencies are complete. Where no dependency exists, prefer delivering highest user value first.

Dependency graph format:

```
Slice 1: [Name] — no dependencies
Slice 2: [Name] — depends on Slice 1
Slice 3: [Name] — depends on Slice 1
Slice 4: [Name] — depends on Slice 2, Slice 3
```

### Step 4: Validate Slice Scope

For each slice, confirm:
- It has at least one acceptance criterion
- It has a clear layer boundary (what layers it touches)
- It is small enough to implement and verify in a single focused session
- It does not contain hidden sub-behaviors that should be their own slice

Split slices that are too large. Merge slices that are trivially small and have no independent value.

---

## Output Format

```markdown
# Vertical Slices: [Feature Name]

## Slice 1: [Name]
**Behavior**: [One-sentence description of what this slice delivers]
**Layers**: domain / application / infrastructure / UI
**Dependencies**: none
**Acceptance criteria**: [list the criteria this slice satisfies]

## Slice 2: [Name]
**Behavior**: [One-sentence description]
**Layers**: application / infrastructure
**Dependencies**: Slice 1
**Acceptance criteria**: [list]

## Execution sequence
1. Slice 1
2. Slice 2
3. Slice 3
```

---

## Rules

- Never plan horizontal slices ("implement all domain entities", "wire all DI")
- Every slice must be independently verifiable — if it can't be tested alone, it's not a slice
- Dependencies must be explicit — implicit ordering causes integration surprises
- Slices are sequenced, not parallelized — implement one at a time to keep context tight
- If a feature cannot be sliced (it is genuinely atomic), treat it as a single slice
