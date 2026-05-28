---
name: verification
description: Use to validate correctness and regression safety of a completed slice or feature. Runs the full validation pipeline, detects regressions, and confirms acceptance criteria are satisfied.
---

## Purpose

Validate that an implementation is correct, complete, and safe to proceed. Verification catches regressions, integration failures, and acceptance criteria gaps before they reach delivery.

---

## When to Use

- After each slice is implemented (per-slice verification)
- Before delegating to delivery-agent (final feature verification)
- When verification-agent orchestrates a high-risk validation pass

---

## Verification Pipeline

Run steps in this order. Stop and report on any failure — do not proceed to the next step.

```
1. lint          — catch style and static analysis errors
2. type-check    — catch type contract violations
3. tests         — run all tests, confirm all pass
4. integration   — validate cross-layer wiring if applicable
```

### Discovering Commands

Check in this order:

1. `AGENTS.md` at project root — authoritative if present
2. `package.json` `scripts` section
3. Framework detection from dependencies:
   - `@angular/core` → `ng lint`, `ng test --watch=false`, `ng build`
   - `vitest` → `npx vitest run`
   - `jest` → `npx jest`
   - `next` → `next lint`, `next build`
   - Generic → `tsc --noEmit`, configured linter, configured test runner

---

## Process

### Step 1: Run Lint

Run the project's linter. On failure:

- Report every violation with file and line
- Do not proceed to type-check until lint passes

### Step 2: Run Type-Check

Run strict type verification. On failure:

- Report every type error with file, line, and error message
- Do not proceed to tests until type-check passes

### Step 3: Run Tests

Run all tests. On failure:

- Separate new failures from pre-existing failures
- For each new failure: classify as regression or slice implementation gap
- Report a clear summary

### Step 4: Validate Acceptance Criteria

After all tests pass:

1. Read each acceptance criterion for the slice/feature
2. Confirm a passing test covers each one
3. Flag any criterion with no test coverage

### Step 5: Detect Regressions

Compare test results against the known baseline (tests that were passing before this slice):

- A test that newly fails is a regression — it must be fixed before proceeding
- A test that was already failing before this slice is pre-existing debt — flag it but do not block

---

## Output Format

```markdown
# Verification Report

## Lint
Status: PASS | FAIL
Violations: [count]
[list violations if any]

## Type-check
Status: PASS | FAIL
Errors: [count]
[list errors if any]

## Tests
Status: PASS | FAIL
Passed: [X] / Total: [Y]
New failures: [list]
Pre-existing failures: [list]

## Acceptance Criteria
Status: COVERED | GAPS FOUND
Gaps: [list criteria with no test coverage]

## Regressions
Status: NONE | FOUND
[list regressions if any]

## Overall
Status: PASS | FAIL | PARTIAL
Recommendation: [proceed / fix before proceeding / escalate]
```

---

## On Failure

Classify the failure and return to the appropriate phase:

| Failure type | Return to |
| ------------ | --------- |
| Implementation error | implementation skill |
| Missing acceptance criteria coverage | tdd skill (add tests) |
| Architectural violation | architecture-review |
| Regression in unrelated area | investigate before proceeding |

---

## Rules

- Never mark a slice verified if any acceptance criterion lacks test coverage
- Never ignore regressions — classify and report even if not blocking
- Never proceed past a lint or type-check failure — fix it first
- Report clearly: distinguish new failures from pre-existing issues
