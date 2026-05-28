---
name: tdd
description: Use to drive implementation through tests. When invoked by feature-lifecycle-agent, authors failing tests before implementation begins. When invoked by verification-agent, executes tests and confirms behavioral correctness.
---

## Purpose

Drive implementation through behavioral verification. Tests are written before code — the failing test is the specification that implementation must satisfy.

---

## Invocation Context

This skill is used differently depending on who invokes it:

| Caller | Phase | Intent |
| ------ | ----- | ------ |
| feature-lifecycle-agent | Before implementation | Author failing tests from spec/criteria |
| verification-agent | After implementation | Execute tests and confirm behavioral correctness |

---

## Authoring Tests (feature-lifecycle-agent context)

### Step 1: Derive Test Scenarios

From the acceptance criteria and slice definition, derive test scenarios:

- One test per distinct behavior
- One test per failure mode
- One test per edge case
- One test per invariant

### Step 2: Write Failing Tests

For each scenario, write a test that:

1. Sets up the precondition (Given)
2. Performs the action (When)
3. Asserts the observable outcome (Then)

The test must fail before implementation exists.

### Test Naming

```
it('should [observable outcome] when [condition]')
it('should return empty list when no items match the filter')
it('should throw NOT_FOUND when the id does not exist')
it('should NOT call the repository when the input is invalid')
```

Always: condition + expectation. Never: `it('works')`, `it('test case 1')`.

### Test Quality Rules

| Rule | Good | Bad |
| ---- | ---- | --- |
| Assert specific values | `expect(result).toBe(42)` | `expect(result).toBeDefined()` |
| Assert behavior, not implementation | Test returned value | Test internal method call order |
| Deterministic | No reliance on timing or order | `setTimeout`, random data |
| Mutation-resistant | Catches removed validation | Passes if condition is inverted |
| One behavior per test | Single assertion per test | Multiple unrelated assertions |

### Mock Strategy

Mock only at system boundaries:

- **Mock**: external HTTP clients, databases, file system, external services
- **Do not mock**: domain logic, value objects, pure functions, deterministic helpers

Use the project's mocking library (discovered from existing test files).

### Step 3: Confirm Tests Fail

Run the tests before handing off to implementation. Confirm:

- Tests fail for the right reason (missing implementation, not syntax error)
- Failure message clearly describes what is missing

---

## Executing Tests (verification-agent context)

### Step 1: Run the Test Suite

Run all tests for the slice being verified. Discover the test command from project conventions:

| Signal | Command |
| ------ | ------- |
| `vitest` in package.json | `npx vitest run` |
| `jest` in package.json | `npx jest` |
| Angular project | `ng test --watch=false` |
| Custom script | Use the project's `test` script |

### Step 2: Evaluate Results

For each failing test:

- Is the test correct and the implementation wrong? → Implementation must be fixed
- Is the test wrong (asserting the wrong thing)? → Flag for review, do not silently fix

### Step 3: Confirm Behavioral Correctness

After all tests pass:

1. Read each acceptance criterion
2. Confirm a passing test covers each criterion
3. If a criterion has no test coverage: flag as a gap — do not consider the slice verified

### Step 4: Report

Output a verification report:

```
Tests: [X passed / Y failed]
Coverage gaps: [list any acceptance criteria with no test]
Regressions: [list any previously passing tests now failing]
Status: PASS | FAIL | PARTIAL
```

---

## Rules

- Never write tests after implementation — tests must come first
- Never change a test to make it pass — fix the implementation instead
- Never skip edge cases — if the acceptance criteria define them, they need tests
- Never use snapshot tests as a substitute for explicit assertions
- If a test cannot be made deterministic, flag it rather than leaving it flaky
