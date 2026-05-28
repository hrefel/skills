---
name: implementation
description: Use when executing a development slice. Implements feature logic guided by TDD output and acceptance criteria, respecting architecture boundaries and project conventions.
---

## Purpose

Execute a single implementation slice safely and consistently. Implementation is guided by failing tests (from the tdd skill) and constrained by acceptance criteria and architecture findings.

---

## Prerequisites

Before starting implementation:

- Failing tests exist for this slice (produced by the tdd skill)
- Acceptance criteria are defined for this slice
- Architecture findings (if any) from architecture-review are available as constraints
- The slice is the current focus — do not implement multiple slices simultaneously

---

## Process

### Step 1: Read the Spec

Before writing any code:

1. Read the failing tests — they are the specification
2. Read the acceptance criteria for this slice
3. Read any architecture constraints flagged for this slice
4. Identify the layer(s) this slice touches

### Step 2: Implement the Minimum

Write the minimum code that makes the failing tests pass. Do not:

- Add behavior not covered by a test
- Implement edge cases not specified in the acceptance criteria
- Introduce abstractions or patterns not required by the current slice
- Import across architecture boundaries

### Step 3: Run Tests After Every Change

After each meaningful code change:

1. Run the tests for the current slice
2. If tests fail: fix the implementation, not the tests
3. If a test was wrong (it tests the wrong thing): flag it — do not silently change it

### Step 4: Validate Against Acceptance Criteria

After all tests pass:

1. Read each acceptance criterion for this slice
2. Verify each one is satisfied by the implementation
3. If a criterion is not covered: add a test and implement the missing behavior

### Step 5: Surface Blockers

If you encounter a blocker:

- Missing dependency or port interface
- Ambiguous business rule
- Architecture constraint that prevents a clean implementation
- Conflicting requirements

**Stop immediately and report the blocker.** Do not work around it silently or guess.

### Step 6: Mark Done

A slice is done when:

- All tests for the slice pass
- All acceptance criteria for the slice are satisfied
- No architecture boundaries are violated
- The code follows project conventions

---

## Architecture Constraints

| Layer | Allowed imports | Forbidden |
| ----- | --------------- | --------- |
| Domain | other domain types only | framework code, I/O, async |
| Application | domain, ports | infrastructure adapters, UI |
| Infrastructure | ports, external libraries | domain business logic |
| UI | application layer hooks/stores | direct infrastructure or domain |

Discovered project conventions override these defaults. Always check project documentation first.

---

## Rules

- Tests drive implementation — never implement behavior that has no test
- Never silence a failing test by deleting it or changing its assertion
- Never add features beyond what the current slice requires
- Never cross architecture layer boundaries
- Surface blockers immediately — do not silently work around constraints
- One slice at a time — do not begin the next slice until the current one is verified complete
