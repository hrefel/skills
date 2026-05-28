---
name: requirement-clarification
description: Use when a feature request contains ambiguity, missing business rules, undefined states or enums, unknown actors, or conflicting requirements. Resolves unknowns before planning or implementation begins.
---

## Purpose

Resolve all ambiguity in a feature request before any planning or implementation starts. Unresolved requirements cause mid-implementation rework — this skill prevents that.

---

## When to Use

- Feature request uses vague language ("manage", "handle", "support")
- Business rules are implicit or assumed
- State transitions, enum values, or constraints are undefined
- Multiple interpretations of a requirement are possible
- Requirements conflict with existing behavior

---

## Process

### Step 1: Scan for Ambiguity

Read the full feature request and identify gaps across these categories:

| Category | Look for |
| -------- | -------- |
| State machine | Entities with status fields — are all transitions defined? |
| Enum values | Typed or sealed fields — are all valid values listed? |
| Actor boundaries | Who can do what? Are permission rules stated? |
| Business rules | Validation logic, thresholds, constraints — are they explicit? |
| API surface | If backend integration is needed, are endpoints known? |
| Error handling | What happens on failure? Are rejection conditions defined? |
| Edge cases | Boundary inputs, concurrent actions, empty states |
| Codebase reuse | Are there existing components, services, or patterns that apply? |

### Step 2: Separate Blockers from Defaults

Classify each gap:

- **Blocker**: Cannot proceed without an answer. Example: undefined state transitions, unknown enum values for sealed types.
- **Default applicable**: A sensible default exists and can be assumed with explicit documentation. Example: pagination page size, sort direction.

### Step 3: Apply Defaults

For defaultable gaps, apply the default and document it as an explicit assumption. Do not ask the user about defaults unless the project context makes the default risky.

### Step 4: Batch Questions

Present all remaining blocker questions in a **single batch**. Never ask one question at a time — this wastes the user's time and breaks flow.

Format:

```
Before I start, I need to clarify a few things:

1. [Question about state machine / enum / rule]
2. [Question about actor boundary]
3. [Question about API surface]

Defaults I'm applying (confirm or override):
- [Default A]: [assumed value]
- [Default B]: [assumed value]
```

### Step 5: Incorporate Answers

Once the user responds, integrate their answers and update the requirements before passing to planning or implementation.

---

## Outputs

- **Clarified requirements**: The feature request rewritten with all ambiguity resolved
- **Assumptions log**: Explicit list of defaults applied with their values
- **Unresolved questions**: Any items the user deferred or did not answer (flag as risk)

---

## Rules

- Never assume a state machine is complete unless all transitions are explicitly stated
- Never proceed with conflicting requirements — surface the conflict, ask for resolution
- Batch all questions — never ask one at a time
- Document every assumption — silent defaults become bugs
- If a requirement cannot be resolved and no default is safe, block and escalate
