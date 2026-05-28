---
name: acceptance-criteria
description: Use when converting clarified requirements into measurable, testable completion conditions. Produces the acceptance checklist that defines when a feature is done.
---

## Purpose

Convert clarified requirements into verifiable completion conditions. Acceptance criteria are the contract between intent and implementation — they define done.

---

## When to Use

- After requirement-clarification, before vertical-slice-planning
- When validating whether a completed slice meets its definition of done
- When establishing the verification targets for a feature

---

## Process

### Step 1: Identify Behavioral Expectations

For each requirement, define what the system must do — not how. Focus on observable outcomes:

- What the user sees or receives
- What state changes occur
- What errors or rejections are produced
- What side effects are triggered

### Step 2: Write Criteria in Given/When/Then Format

Each criterion must follow this structure:

```
Given [precondition / system state]
When [action or event]
Then [observable outcome]
```

Examples:
```
Given a user is authenticated
When they submit a valid form
Then the record is saved and a success message is shown

Given a user has no admin role
When they attempt to access the admin panel
Then they receive a 403 response
```

### Step 3: Cover All Paths

For each feature area, ensure coverage of:

| Path type | Description |
| --------- | ----------- |
| Happy path | The intended successful flow |
| Failure paths | Invalid inputs, permission denials, business rule violations |
| Edge cases | Boundary values, empty states, concurrent actions |
| Non-functional | Performance thresholds, accessibility, security constraints (if specified) |

### Step 4: Assign to Slices

Once vertical-slice-planning produces slice definitions, map each acceptance criterion to a specific slice. Every slice must have at least one criterion — if it doesn't, the slice has no definition of done and should not be implemented.

---

## Output Format

```markdown
# Acceptance Criteria: [Feature Name]

## Slice: [Slice Name]

- [ ] Given [state], when [action], then [outcome]
- [ ] Given [state], when [action], then [outcome]

### Edge Cases
- [ ] Given [boundary condition], when [action], then [outcome]

### Failure Modes
- [ ] Given [invalid state], when [action], then [error outcome]
```

---

## Rules

- Each criterion must be independently verifiable — no compound "and" criteria that hide two behaviors in one
- Use domain language, not technical implementation language
- Criteria must be testable by a human or automated test without access to internal state
- A criterion that cannot be verified is not a criterion — rewrite it until it can be checked
- Do not include implementation details (no "the database should...", "the API should return HTTP 200...")
