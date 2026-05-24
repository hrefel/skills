# Feature Lifecycle Skill

Build a complete feature from requirements to working code using a structured 9-phase pipeline with BDD specs and test-first implementation.

## What It Does

This skill drives the full lifecycle of building a self-contained feature:

1. **Decompose** requirements into numbered micro BDD spec files
2. **Implement** across every architecture layer — domain, ports, application, infrastructure, DI, UI
3. **Verify** with lint, type-check, and tests at every stage

It discovers all conventions from the project itself — no hardcoded assumptions about frameworks, languages, or folder structure.

## When to Use

| Scenario | Use this skill? |
|----------|----------------|
| "Build the pulse issues feature" | Yes |
| "Implement this requirements doc" | Yes |
| "Scaffold this feature" (dry-run) | Yes |
| "Execute phase 3" (specific phase) | Yes |
| "Write tests for existing code" | No — use `test-writer` |
| "Build a single component" | No — use `component-scaffold` |
| "Fix a bug" | No — use `debug-error` |

## The 9-Phase Pipeline

```
Phase 0: DISCOVER       — Clarify ambiguous requirements
Phase 1: SPEC           — Decompose requirements into numbered micro BDD specs
Phase 2: DOMAIN         — Entities, value objects, validation (TDD)
Phase 3: PORTS          — Interface contracts (no tests)
Phase 4: APPLICATION    — Use cases, stores, pure helpers (TDD)
Phase 5: INFRASTRUCTURE — Adapter implementations
Phase 6: DI             — Dependency injection wiring
Phase 7: UI             — Hook binding → components → page (optional)
Phase 8: VERIFY         — lint → type-check → tests
```

Each phase is independently executable. You can run all phases sequentially or target a specific one.

## How to Activate

In OpenCode, the skill activates automatically when your request matches its description. You can also trigger it explicitly:

```
> Use the feature-lifecycle skill to build the user authentication feature
```

Or target specific phases:

```
> Execute phase 1 for the orders feature
> Run phase 2-4 for the dashboard feature
> Dry-run the payments feature
```

## Phase Details

### Phase 0: DISCOVER

Resolves ambiguous requirements before generating specs. Asks clarifying questions about state machines, enum values, action buttons, column display, filters, API surface, and codebase reuse.

**Skip when**: the prompt is fully specified or the user says "skip discovery" / "use defaults".

### Phase 1: SPEC

Decomposes the feature into testable subjects (one per file) and generates numbered micro spec files in BDD format.

Output location: `requirements/<feature>/<subfeature>/`

```
requirements/orders/
├── 01-orderEntity.md
├── 02-orderStatus.md
├── 03-createOrderUseCase.md
├── ...
└── NN-columnMapping.md
```

### Phase 2: DOMAIN

Implements domain entities, value objects, and validation. Pure input/output — no mocks, no framework imports, no async.

### Phase 3: PORTS

Defines interface contracts that the application layer depends on. No tests needed — interfaces are excluded from coverage.

### Phase 4: APPLICATION

Implements use cases, stores/state management, and pure business helpers. Test-first. Mocks only port interfaces.

### Phase 5: INFRASTRUCTURE

Implements port interfaces as concrete adapters (HTTP clients, database repos). Never exposes transport details upstream.

### Phase 6: DI

Registers the feature's dependencies in the project's DI container. Registration only — no business logic.

### Phase 7: UI (Optional)

Builds presentational components, reactive hooks, and page composition. Only activates when the feature has a user-facing surface.

### Phase 8: VERIFY

Runs the project's verification pipeline: lint → type-check → tests → build.

## Dry-Run Mode

When you say "scaffold", "dry-run", or "plan only":

1. Runs architecture discovery
2. Generates all spec files with full BDD format
3. Creates empty file scaffolds with correct names and locations
4. Outputs a summary of planned files and dependencies
5. **Does not write any implementation code**

## Spec Format

Each spec file follows this structure:

| Section | Purpose |
|---------|---------|
| **Subject** | The public API surface being specified |
| **Purpose** | Domain language — why this behavior exists |
| **Dependencies** | External (mock) vs internal (collaborator) |
| **Invariants** | Rules that must hold in every state |
| **Out of Scope** | What the feature explicitly does NOT handle |
| **Observable Behaviors** | What the system does (returned values, state changes, errors) |
| **Internal Collaborations** | When and how dependencies are called |
| **Edge Cases** | Expected behavior for boundary conditions |
| **Properties** | Cross-cutting guarantees (determinism, idempotency) |
| **Examples** | Concrete input/output pairs |
| **Failure Modes** | Every error state the system can produce |

## Key Principles

- **Assert behavior, not implementation** — test returned values and state changes, not internal method calls
- **Mock only boundaries** — never mock pure functions, domain logic, or value objects
- **Smallest satisfying code** — no speculative abstractions, no unnecessary layers
- **Verify after every phase** — catch errors early, not at the end
- **Adapt to the project** — never impose architecture the project doesn't use

## Related Skills

| Skill | When to use instead |
|-------|-------------------|
| `bdd` | For a single unit — `feature-lifecycle` orchestrates across all layers |
| `test-writer` | For adding tests to existing code |
| `component-scaffold` | For building a single component |
| `code-review` | After completing a feature, to review before committing |
