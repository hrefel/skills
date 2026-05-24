---
name: bdd
description: Generate behavioral specs and drive TDD implementation for new features. Use for BDD, TDD, spec-first, or Gherkin-driven development.
---

## What I do

I transform feature requests into structured behavioral specifications, then drive implementation through test-first development.

**Primary output**: a behavioral specification document that captures intent, invariants, observable behaviors, edge cases, and failure modes.

**Secondary output**: failing tests derived from the spec, then minimal implementation that satisfies them.

I adapt to the project's existing test framework, language, and conventions.

## When to use me

- When the user asks to implement a feature using TDD or BDD
- When the user says "write tests first", "test-driven", or "behavior-driven"
- When the user provides Gherkin scenarios (Given/When/Then) or `.feature` files
- When the user wants to design a feature from requirements before coding
- When the user asks for executable specifications or a behavioral spec
- When the user says "spec this feature" or "define the behavior"

Do NOT use me when the user just wants to write tests for existing code — use `test-writer` instead. I am for driving *new* implementation from specs.

## How I work

1. **Discover conventions** — Find the project's test framework, file locations, naming patterns, assertion style, and existing test structure
2. **Understand intent** — Extract business purpose, domain rules, invariants, boundaries, failure scenarios, and dependencies from the user's request
3. **Generate behavioral spec** — Produce a spec document using the format below
4. **Get confirmation** — Present the spec to the user; iterate on feedback. If no feedback is given within one exchange, proceed.
5. **Generate failing tests** — Derive tests from the spec; confirm they fail for the right reason. Evaluate mutation resistance as you write: would each test catch an inverted condition, removed validation, or deleted branch? Strengthen tests that wouldn't.
6. **Generate minimal implementation** — Smallest code that satisfies the spec; no speculative abstractions
7. **Run tests** — Confirm all pass
8. **Refactor if needed** — Only after green tests; preserve public contract and observable behavior

## Behavioral Specification Format

Use this format when generating specs. Include every section. If a section has no content, write "None identified" — do not omit it.

```md
# Subject
[ClassName.methodName | FunctionName]

---

# Purpose
[Business/domain intent — why this behavior exists]

---

# Dependencies

## External Dependencies
- [Dependency.method()] — [what it provides]

## Internal Collaborators
- [Internal helper/function] — [what it does]

---

# Invariants
- [Rule that must always remain true]
- [Constraint that must never be violated]

---

# Out of Scope
- [Explicitly unsupported behavior]
- [What this feature does NOT handle]

---

# Observable Behaviors

## When [specific condition/state]
- it should [observable result]
- it should [observable side effect]

---

# Internal Collaborations

## When [specific condition/state]
- it should call [Dependency.method()] with [arguments]
- it should NOT call [Dependency.method()]

---

# Edge Cases
- [Boundary condition]: [expected behavior]
- [Unexpected input]: [expected behavior]
- [Invalid value]: [expected behavior]

---

# Properties
- [Deterministic behavior — same input always produces same output]
- [Ordering guarantee if applicable]
- [Idempotency if applicable]

---

# Examples

| Input | Expected Output |
|---|---|
| [input] | [output] |
| [input] | [output] |

---

# Failure Modes

| Scenario | Expected Error |
|---|---|
| [condition] | [error] |
| [condition] | [error] |
```

### Section guidelines

- **Subject**: The public API surface being specified — a class method, function, or endpoint
- **Purpose**: Domain language, not technical language. Answer "why does this exist?"
- **Dependencies**: Distinguish external (mock these) from internal (these are implementation collaborators)
- **Invariants**: Rules that must hold in every state and every code path
- **Out of Scope**: Features or inputs the system explicitly does not handle — e.g. "does not support bulk operations", "does not validate email format". Prevents scope creep and sets test boundaries
- **Observable Behaviors**: The core of the spec. What the system *does* — returned values, state changes, emitted events, thrown errors. Not *how*
- **Internal Collaborations**: Interaction expectations — when and how dependencies are called. Lower priority than observable behaviors
- **Edge Cases**: Include expected behavior for each case, not just a list of inputs
- **Properties**: Cross-cutting guarantees that apply across all behaviors
- **Examples**: Concrete input/output pairs that make the spec immediately verifiable
- **Failure Modes**: Every way the system can reject invalid input or handle error states

## BDD-Specific: Gherkin Integration

When the project uses Cucumber, Jest Cucumber, Behave, or similar BDD tooling — detected by the presence of `.feature` files or `cucumber`/`behave`/`jest-cucumber` in dependencies:

- Accept or generate `.feature` files with `Given / When / Then` structure
- Map each `When/Then` pair to a row in the Observable Behaviors section
- Keep step definitions thin — delegate to domain functions, not implementation details
- One scenario per behavior; avoid mega-scenarios
- The spec format above can be derived from Gherkin scenarios and vice versa

## Core Rules

### Test quality

- **Assert behavior, not implementation** — test returned values, state changes, thrown errors, visible side effects; NOT internal method calls, private methods, or framework internals
- **Explicit assertions** — never `toBeDefined()`, `toBeTruthy()`, or snapshot abuse when a precise value can be asserted
- **Deterministic** — no reliance on execution order, timing, or external state
- **Mutation-resistant** — tests must catch inverted conditions, removed validation, deleted branches

### Mocking

Mock only infrastructure boundaries (network, filesystem, database, external services). Never mock pure functions, domain logic, value objects, or deterministic transformations.

### Implementation

Generate the smallest code that satisfies the spec. Avoid:
- Speculative abstractions
- Unnecessary layers or indirection
- Premature optimization
- Overengineering

### Architecture awareness

If the project follows Clean Architecture or similar:
- Keep domain tests framework-independent
- Test use cases for orchestration correctness
- Keep infrastructure replaceable

Do not impose architecture the project doesn't use — adapt to what exists.

## Test Naming

```
it('should return 0m when milliseconds is negative')   // Good
it('test negative case')                                // Bad
```

Describe condition + expectation in every test name.

## Edge Case Awareness

Evaluate only the edge cases relevant to the feature:
- null / undefined / empty / malformed input
- boundary values (zero, negative, overflow)
- ordering, duplicates, idempotency
- concurrency (if applicable)

## Relationship to test-writer skill

- **`bdd` (this skill)**: Generates behavioral specs and drives *new feature implementation* from them. Tests first, then code.
- **`test-writer`**: Adds tests to *existing code*. Reads implementation, then writes coverage.

Use `bdd` when building something new. Use `test-writer` when covering existing code.
