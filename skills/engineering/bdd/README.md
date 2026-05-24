# BDD (Behavior-Driven Development) Skill

Generate behavioral specifications and drive test-first implementation for individual units. Produces structured specs, then writes failing tests and minimal implementation to satisfy them.

## What It Does

This skill transforms feature requests into structured behavioral specifications, then drives implementation through test-first development:

1. **Spec** — Produces a behavioral specification covering intent, invariants, observable behaviors, edge cases, and failure modes
2. **Test** — Generates failing tests derived from the spec
3. **Implement** — Writes the smallest code that satisfies the spec
4. **Refactor** — Only after green tests; preserves public contract and observable behavior

It adapts to the project's existing test framework, language, and conventions.

## When to Use

| Scenario | Use this skill? |
|----------|----------------|
| "Implement this feature using TDD" | Yes |
| "Write tests first for the calculator" | Yes |
| "Spec this function" | Yes |
| "Drive implementation from these requirements" | Yes |
| User provides Gherkin `.feature` files | Yes |
| "Write tests for my existing code" | No — use `test-writer` |
| "Build a full feature end-to-end" | No — use `feature-lifecycle` |

## How to Activate

The skill activates automatically when your request mentions TDD, BDD, test-first, or spec-driven development. You can also trigger it explicitly:

```
> Use the bdd skill to implement the formatDuration function
```

Or provide Gherkin scenarios:

```
> Implement these scenarios using BDD:
>   Given a user with name "Alice"
>   When the greeting is generated
>   Then it should return "Hello, Alice"
```

## Workflow

```
1. Discover conventions  → Find test framework, naming patterns, assertion style
2. Understand intent     → Extract business rules, invariants, failure scenarios
3. Generate spec         → Produce behavioral spec (see format below)
4. Get confirmation      → Present spec; iterate on feedback
5. Generate tests        → Write failing tests from the spec
6. Generate code         → Write minimal implementation
7. Run tests             → Confirm all pass
8. Refactor              → Only after green; preserve observable behavior
```

## Spec Format

Every behavioral specification follows this structure:

| Section | What to write |
|---------|--------------|
| **Subject** | The public API surface — a class method, function, or endpoint |
| **Purpose** | Domain language answering "why does this exist?" |
| **Dependencies** | External (mock these) vs internal (implementation collaborators) |
| **Invariants** | Rules that must hold in every state and every code path |
| **Out of Scope** | What the system explicitly does NOT handle — prevents scope creep |
| **Observable Behaviors** | What the system *does* — returned values, state changes, thrown errors |
| **Internal Collaborations** | When and how dependencies are called |
| **Edge Cases** | Expected behavior for each boundary condition |
| **Properties** | Cross-cutting guarantees — determinism, ordering, idempotency |
| **Examples** | Concrete input/output pairs for immediate verification |
| **Failure Modes** | Every way the system can reject invalid input or handle errors |

Every section must be present. If a section has no content, write "None identified".

## Gherkin Integration

When the project uses Cucumber, Jest Cucumber, Behave, or similar tooling (detected by `.feature` files or relevant dependencies):

- Accepts or generates `.feature` files with `Given / When / Then` structure
- Maps each `When/Then` pair to a row in the Observable Behaviors section
- Keeps step definitions thin — delegates to domain functions, not implementation details
- One scenario per behavior; avoids mega-scenarios

## Key Principles

### Test Quality

- **Assert behavior, not implementation** — test returned values, state changes, thrown errors; not internal method calls or private methods
- **Explicit assertions** — never `toBeDefined()`, `toBeTruthy()`, or snapshot abuse when a precise value can be asserted
- **Deterministic** — no reliance on execution order, timing, or external state
- **Mutation-resistant** — tests must catch inverted conditions, removed validation, deleted branches

### Mocking

Mock only infrastructure boundaries (network, filesystem, database, external services). Never mock:
- Pure functions
- Domain logic
- Value objects
- Deterministic transformations

### Implementation

Generate the smallest code that satisfies the spec. Avoid:
- Speculative abstractions
- Unnecessary layers or indirection
- Premature optimization

### Test Naming

```
it('should return 0m when milliseconds is negative')    // Good — condition + expectation
it('test negative case')                                 // Bad — vague
```

## Edge Cases to Consider

- `null` / `undefined` / empty / malformed input
- Boundary values (zero, negative, overflow)
- Ordering, duplicates, idempotency
- Concurrency (if applicable)

## Related Skills

| Skill | When to use instead |
|-------|-------------------|
| `feature-lifecycle` | For building a full feature across all architecture layers |
| `test-writer` | For adding tests to existing, already-implemented code |

Use `bdd` for driving **new** implementation from specs. Use `test-writer` for covering **existing** code.
