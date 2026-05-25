---
name: feature-lifecycle
description: Use when building a new feature end-to-end from requirements, implementing a feature spec across architecture layers, executing a specific phase of a multi-layer feature pipeline, or scaffolding a dry-run file structure. Stack-agnostic and language-agnostic.
---

## Overview

Drives the **full lifecycle of building a self-contained feature**: from decomposing requirements into BDD specs, through test-first implementation across every architecture layer, to DI wiring and page composition.

**Primary output**: numbered micro BDD spec files capturing every testable subject.

**Secondary output**: working implementation across all layers — domain → ports → application → infrastructure → DI → UI — driven by those specs.

All conventions are discovered from the project itself. No hardcoded assumptions about frameworks, languages, or folder structure.

## When to Use

- When the user asks to build a new feature end-to-end ("build the pulse issues feature")
- When the user provides a requirements document or feature spec and says "implement this"
- When the user says "execute phase N" to run a specific phase of the pipeline
- When the user says "scaffold this feature" for a dry-run of file structure
- When the user has BDD spec files and wants implementation driven from them

## When NOT to Use

- Adding tests to **existing** code → use `test-writer`
- Building a single component → use `component-scaffold`
- Fixing a bug or debugging → use `debug-error` or `systematic-debugging`
- Refactoring existing code → use `refactor-safe`

## How it Works

Runs a **9-phase pipeline** (Phase 0 through Phase 8). Each phase is independently executable. The user can run all phases sequentially or target a specific phase.

```
Phase 0: DISCOVER      — Clarify ambiguous requirements (question catalog in DISCOVER.md)
Phase 1: SPEC          — Decompose requirements → numbered micro BDD specs
Phase 2: DOMAIN        — Entities, value objects, validation (TDD)
Phase 3: PORTS         — Interface contracts (no tests)
Phase 4: APPLICATION   — Use cases, stores, pure helpers (TDD)
Phase 5: INFRASTRUCTURE — Adapter implementations
Phase 6: DI            — Dependency injection wiring
Phase 7: UI            — Hook binding → components → page (optional)
Phase 8: VERIFY        — lint → type-check → tests
```

**Dry-run mode**: when the user says "scaffold", "dry-run", or "plan only", I generate all spec files and file scaffolds (empty files with correct names and locations) without writing implementation.

---

## Architecture Discovery Protocol

Before Phase 1, I discover the project's conventions by reading documentation files. I search for these in order:

| File | What I extract |
|------|---------------|
| `AGENTS.md` (project root) | Architecture layers, verification commands, commit format, path aliases |
| `src/<layer>/AGENTS.md` (each layer) | Max file lines, allowed/forbidden imports, naming conventions, file structure |
| `docs/FDD/AGENTS.md` | FDD folder scaffold, layer rules, naming conventions, forbidden patterns |
| `docs/FDD/GUIDE.md` | Full annotated examples, templates, testing patterns |
| `package.json` | Test framework, dependencies (detects solid-js, react, vue, etc.) |
| `requirements/<feature>/template.md` | Project-specific spec template format (if exists) |
| Test config (`vitest.config.*`, `jest.config.*`, etc.) | Test globals, coverage settings, file patterns |

**If no AGENTS.md files exist**, I fall back to:
- Discovering layer structure from `src/` directory listing
- Detecting test framework from `package.json` devDependencies
- Inferring naming conventions from existing files in each layer
- Using sensible defaults (entities as interfaces, ports as `I{Name}` interfaces, etc.)

---

## Phase 0: DISCOVER

### What happens

Before generating specs, resolve any ambiguity by asking the user clarifying questions. This prevents mid-implementation back-and-forth.

### Process

1. Read the user's feature request and requirements
2. Check whether the feature can be expressed as an FDD name (`<Action> <Result> <Object>`). If not, include this as the **first question** in the batch — the canonical name must be confirmed before Phase 1
3. Run the Architecture Discovery Protocol to learn what's already answerable from project docs
4. Identify ambiguous inputs using the **question catalog** in `DISCOVER.md` (located alongside this file)
5. Skip questions already answerable from the user's prompt or project conventions
6. Present remaining questions in a **single batch** (never one-at-a-time)
7. Incorporate answers into spec generation in Phase 1

### When to skip this phase

- The user's prompt is fully specified (all enums, states, actions, columns, and filters are defined)
- The feature is small enough that no ambiguity exists (e.g., a single utility function)
- The user says "skip discovery" or "use defaults"

### Question categories (from DISCOVER.md)

| Category | Ask when... | Priority |
|----------|-------------|----------|
| State Machine | Feature has state-based entities | 🔴 Blocker |
| Enum Values | Feature has typed/sealed fields | 🔴 Blocker |
| Action Buttons | Feature has a data table with row actions | 🔴 Blocker |
| Column Display | A column's rendering is ambiguous | 🟡 Default |
| Filter Behavior | Feature has a filterable list | 🟡 Default |
| API Surface | Feature talks to a backend, endpoint unknown | 🟡 Default |
| Codebase Reuse | Feature touches concerns that may already exist | 🟡 Default |

Full question catalog and defaults are in `DISCOVER.md`.

---

## Phase 1: SPEC

### What happens

1. Read the user's requirements (inline text, file, or feature description)
2. Discover project conventions via the Architecture Discovery Protocol
3. Decompose the feature into **testable subjects** — one subject per file
4. Generate numbered micro spec files

### Feature Naming (FDD)

Every feature must have a canonical name following the FDD rule:

```
<Action> <Result> <Object>
```

Examples: `Calculate Total Price` · `Approve Leave Request` · `Generate Customer Statement`

This name is the single source of truth for all downstream naming. Derive everything from it:

| FDD Name | Feature folder | Use case | Domain entity | Port |
|----------|---------------|----------|---------------|------|
| Calculate Total Price | `calculate-total-price/` | `CalculateTotalPriceUseCase` | `Price` | `IPriceRepository` |
| Approve Leave Request | `approve-leave-request/` | `ApproveLeaveRequestUseCase` | `LeaveRequest` | `ILeaveRequestRepository` |
| Generate Customer Statement | `generate-customer-statement/` | `GenerateCustomerStatementUseCase` | `Statement` | `IStatementRepository` |

**Derivation rules:**
- **Object** → domain entity name and port name (`I{Object}Repository`)
- **Action** → use case verb prefix (`{Action}{Object}UseCase`)
- **Result** (when distinct from Object) → response model or output value object name

When no project-specific naming convention exists, this derivation is the default. When a project convention exists, prefer it — but the FDD name still anchors the feature identity.

### Decomposition rules

- **One subject per file**: a class method, a function, a use case, a component, a helper
- **Numbered prefix**: `01-subjectName.md`, `02-anotherSubject.md`, ... `NN-reference.md`
- **Last file for non-testable reference**: column mappings, color constants, configuration tables
- **Group by layer**: domain subjects first, then application, then presentation/UI

### Edge cases and error handling ownership

Phase 1 is where **all** edge cases and error handling are defined — not discovered later during implementation.

| What to define | Where in the spec |
|----------------|-------------------|
| Invalid inputs, boundary values | `# Edge Cases` section |
| Rejection conditions and error types | `# Failure Modes` section |
| Invariants that must never be violated | `# Invariants` section |

**Rule:** If an error scenario is not in a spec file, it must not be silently implemented later. Either add it to the spec or raise it with the user. Implementation phases consume what Phase 1 defines:

| Phase | Error handling responsibility |
|-------|-------------------------------|
| Phase 2 (DOMAIN) | Implement domain validation and invariant violations from spec |
| Phase 4 (APPLICATION) | Implement use case failure paths from `Failure Modes` |
| Phase 5 (INFRASTRUCTURE) | Wrap and transform adapter errors — never leak HTTP details upstream |

### Output location

```
requirements/<feature>/<subfeature>/
├── 01-domainSubject.md
├── 02-domainSubject.md
├── 03-applicationUseCase.md
├── ...
└── NN-columnMapping.md
```

### BDD Spec Format

Use this format for every spec file. Include every section. If a section has no content, write "None identified" — do not omit it.

```md
# Subject
[ClassName.methodName | FunctionName]

---

# Purpose
[Business/domain intent — why this behavior exists]

---

# Dependencies

## External Dependencies (mock these)
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

---

# Observable Behaviors

## When [specific condition/state]
- it should [observable result]
- it should [observable side effect]

## When [specific condition/state]
- it should [observable result]
- it should NOT [observable behavior]

---

# Internal Collaborations

## When [specific condition/state]
- it should call [Dependency.method()] with [specific arguments]
- it should NOT call [Dependency.method()]

---

# Edge Cases
- [Boundary condition]: [expected behavior]
- [Unexpected input]: [expected behavior]

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

---

# Failure Modes

| Scenario | Expected Error |
|---|---|
| [condition] | [error code/type] |
```

### Section guidelines

- **Subject**: The public API surface being specified — a class method, function, or endpoint
- **Purpose**: Domain language, not technical. Answer "why does this exist?"
- **Dependencies**: Distinguish external (mock these) from internal (implementation collaborators)
- **Invariants**: Rules that must hold in every state and every code path
- **Out of Scope**: Prevents scope creep — explicitly state what the feature does NOT handle
- **Observable Behaviors**: What the system *does* — returned values, state changes, thrown errors. Not *how*
- **Internal Collaborations**: When and how dependencies are called. Lower priority than observable behaviors
- **Edge Cases**: Include expected behavior for each case, not just a list of inputs
- **Properties**: Cross-cutting guarantees that apply across all behaviors
- **Examples**: Concrete input/output pairs that make the spec immediately verifiable
- **Failure Modes**: Every way the system can reject invalid input or handle error states

---

## Phase 2: DOMAIN

### What happens

Implement domain entities, value objects, validation, and pure functions. **Test-first.**

### Process

1. Read the domain spec files (`01-*.md`, `02-*.md`, etc.)
2. For each spec:
   a. Write tests that cover every "Observable Behaviors" and "Edge Cases" section
   b. Run tests — confirm they fail for the right reason
   c. Write minimal implementation that satisfies the spec
   d. Run tests — confirm they pass
3. Run verification (lint + type-check + tests)

### Rules

- **No mocks** — domain layer is pure input/output
- **No framework imports** — no `solid-js`, `react`, `express`, no I/O
- **No async** — domain is synchronous (if the project conventions say so)
- Assert behavior, not implementation — test returned values, state changes, thrown errors
- Explicit assertions — never `toBeDefined()` or `toBeTruthy()` when a precise value can be asserted
- Mutation-resistant — tests must catch inverted conditions, removed validation, deleted branches

### File placement

Discovered from project conventions. Typical patterns:
- `src/domain/entities/<Name>.ts` or `src/features/<feature>/domain/<name>.ts`
- `src/domain/value-objects/<Name>.ts`
- Tests alongside source: `<Name>.test.ts` or `__test__/<Name>.test.ts`

---

## Phase 3: PORTS

### What happens

Define interface contracts (ports) that the application layer depends on. **No tests needed** — interfaces are excluded from coverage.

### Process

1. Read the application spec files that reference dependencies
2. Extract the port interfaces from "Dependencies" sections
3. Create interface files matching project naming conventions

### Naming conventions (discovered, with defaults)

| Thing | Default convention | Example |
|-------|-------------------|---------|
| Port interface | `I{Name}Repository` or `I{Name}Gateway` | `IPulseIssueRepository` |
| Port file | `<name>Repository.ts` or `<name>Gateway.ts` | `pulseIssueRepository.ts` |
| Method naming | CRUD or domain verbs | `findAll()`, `findById()`, `update()` |

### File placement

Discovered from project conventions. Typical patterns:
- `src/application/ports/I{Name}Repository.ts`
- `src/features/<feature>/ports/<name>Repository.ts`

---

## Phase 4: APPLICATION

### What happens

Implement use cases, stores/state management, and pure business helpers. **Test-first for each sub-phase.**

### Sub-phases

Execute in this order. Each sub-phase writes tests before implementation.

1. **Use cases** — One factory function per business action. Each receives dependencies (ports) and orchestrates them
2. **Store/state** — Framework-agnostic state container. Exposes `getState()` + mutation methods. UI layer wraps in reactive signals
3. **Pure helpers** — Filtering, sorting, formatting, color mapping. No mocks needed

### Process (for each sub-phase)

1. Read the corresponding spec files
2. Write tests covering every "Observable Behaviors", "Internal Collaborations", and "Failure Modes"
3. Run tests — confirm they fail
4. Write minimal implementation
5. Run tests — confirm they pass

### Mock strategy

Mock only port interfaces — implement the interface with test doubles using the project's mocking library (`vi.fn()`, `jest.fn()`, etc.). Never mock pure functions, domain logic, or value objects.

### Output port pattern

Use cases may follow an output port pattern for decoupled presentation:
```
presentSuccess(responseModel) → called on success
presentFailure(error)         → called on failure
```

### File placement

Discovered from project conventions. Typical patterns:
- Use cases: `src/application/use-cases/<verb><Name>UseCase.ts` or `src/features/<feature>/application/<verb><Name>.ts`
- Stores: `src/features/<feature>/application/<name>Store.ts`
- Helpers: `src/features/<feature>/application/<name>Helpers.ts`
- Tests: `__test__/<name>.test.ts` alongside source

---

## Phase 5: INFRASTRUCTURE

### What happens

Implement the port interfaces as concrete adapters (HTTP clients, database repos, external service clients).

### Process

1. Read port interfaces from Phase 3
2. Discover the project's HTTP client pattern (from existing repos)
3. Implement each port method
4. Follow the project's error handling pattern (wrap/transform, never leak HTTP details upstream)

### Rules

- Follow existing adapter patterns in the project
- Never expose HTTP-specific details (status codes, headers, URLs) to the application layer
- Use the project's API client — never raw `fetch` or `axios` if a wrapper exists
- Typically excluded from coverage; tested indirectly through use case tests

### File placement

Discovered from project conventions. Typical patterns:
- `src/infrastructure/repositories/<Name>Repository.ts`
- `src/features/<feature>/adapters/<name>HttpAdapter.ts`

---

## Phase 6: DI

### What happens

Register the new feature's dependencies in the project's DI container.

### Process

Follow the project's DI registration pattern. Common 3-touch pattern:

1. **Create module**: `src/di/modules/<name>.module.ts`
   - Define `<Name>Cradle` interface listing only tokens this module registers
   - Export `register<Name>Module()` returning registration map
2. **Add to types**: Add `<Name>Cradle` to the container's aggregate type
3. **Spread in container**: Add `...register<Name>Module()` to container registration

### Rules

- Registration only — no business logic in DI files
- Register adapters as singletons (or project's default scope)
- Never call `resolve()` inside feature island layers — only at the composition root

### Verification

After wiring, verify that resolution works (type-check catches miswired dependencies).

---

## Phase 7: UI *(optional)*

### When this phase activates

- The spec files include UI subjects (components, pages, helpers)
- The user explicitly requests UI/page composition
- The feature has a user-facing surface

If none of these apply, skip this phase.

### Figma reference (when provided)

If the user provides a Figma URL, extract design context **before** writing any UI code:

1. Call `get_design_context` on the URL — extracts layout structure, component names, spacing, and design tokens
2. Call `get_screenshot` — use as visual ground truth when implementing components
3. Map Figma component names to the project's existing UI primitives (Button, Badge, Table, etc.)
4. Use extracted colors, spacing, and typography values directly — do not guess or invent values

If no Figma URL is provided, infer layout from the spec files and existing project components.

### Figma edge cases

| Situation | Action |
|-----------|--------|
| URL is inaccessible or private | Warn the user, skip Figma extraction, fall back to spec-driven layout |
| `get_design_context` returns empty or partial data | Fall back to `get_screenshot` alone; note which tokens are missing |
| `get_screenshot` fails | Proceed with `get_design_context` data only; note the gap |
| Figma component names don't match any project primitive | Ask the user to map them, or use the closest visual match and add a comment |
| Figma URL points to a specific node vs a full page | Use whichever node is provided; do not navigate to parent frames unless instructed |
| Figma has multiple pages | Use the page the URL links to; ask the user if ambiguous |
| Design tokens (colors, spacing) differ from the project's design system | Prefer the project's existing tokens; flag mismatches for the user to resolve |
| Figma MCP is unavailable | Warn the user, skip Figma extraction entirely, proceed from specs |

### Sub-phases

1. **Reactive binding** — Create a hook/composable that wraps the application store in the project's reactive primitives (signals, refs, observables)
2. **Components** — Build presentational components matching project patterns (cva variants, splitProps, cn for class merging, etc.)
3. **Page composition** — Wire hook + components + existing layout into a page

### Rules

- Components are **presentational only** — no business logic, no data fetching
- All data comes via props from the hook/store
- Use project's existing UI primitives (Button, Badge, Table, etc.)
- Discover component patterns from `src/components/` conventions
- When a Figma reference exists, it is the source of truth for layout and visual design — spec files govern behavior, Figma governs appearance

### File placement

Discovered from project conventions. Typical patterns:
- Hook: `src/features/<feature>/ui/use<Name>.ts` or `src/hooks/use<Name>.ts`
- Components: `src/features/<feature>/ui/<Name>Table.tsx`, `<Name>FilterBar.tsx`
- Page: `src/features/<feature>/ui/<Name>Page.tsx` or `src/presentation/pages/<name>/`

---

## Phase 8: VERIFY

### What happens

Run the project's verification pipeline. Discovered from root `AGENTS.md` or `package.json` scripts.

### Typical verification order

```
1. lint        — catches style/type errors
2. type-check  — strict type verification
3. tests       — all tests pass
4. build       — (optional) if project has a build step
```

### When to verify

- **After every implementation phase** (2, 4, 5, 6, 7) — not just at the end
- This catches import path errors, type mismatches, and lint violations early
- If verification fails, fix before moving to the next phase

---

## Dry-Run Mode

When the user says "scaffold", "dry-run", "plan only", or "--dry-run":

1. Run the Architecture Discovery Protocol
2. Generate all spec files with full BDD format (Phase 1)
3. Create empty file scaffolds with correct names and locations for all phases
4. Output a summary of planned files, their layers, and dependencies
5. **Do not write any implementation code**

This lets the user review the plan, adjust specs, and approve before committing to code.

---

## Phase Isolation

Each phase is independently executable. The user can:

- **"execute phase 3"** — run only the ports phase
- **"execute phase 4"** — run only the application phase (assumes domain + ports exist)
- **"build the full feature"** — run all phases sequentially
- **"dry-run phase 1"** — generate specs only

### Prerequisites between phases

| Phase | Requires |
|-------|----------|
| Phase 0: DISCOVER | Feature request from user |
| Phase 1: SPEC | Clarified requirements from Phase 0 (or fully-specified user prompt) |
| Phase 2: DOMAIN | Spec files from Phase 1 |
| Phase 3: PORTS | Domain entities from Phase 2 |
| Phase 4: APPLICATION | Ports from Phase 3, Domain from Phase 2 |
| Phase 5: INFRASTRUCTURE | Ports from Phase 3 |
| Phase 6: DI | Infrastructure from Phase 5 |
| Phase 7: UI | Application from Phase 4, DI from Phase 6 |
| Phase 8: VERIFY | All implemented phases |

---

## Core Rules

### Test quality

- **Assert behavior, not implementation** — test returned values, state changes, thrown errors; NOT internal method calls or private methods
- **Explicit assertions** — never `toBeDefined()`, `toBeTruthy()`, or snapshot abuse when a precise value can be asserted
- **Deterministic** — no reliance on execution order, timing, or external state
- **Mutation-resistant** — tests must catch inverted conditions, removed validation, deleted branches

### Mocking

- Mock only infrastructure boundaries (network, filesystem, database, external services)
- Never mock pure functions, domain logic, value objects, or deterministic transformations
- Implement port interfaces with lightweight test doubles, not heavy mock frameworks

### Implementation

- Smallest code that satisfies the spec
- No speculative abstractions
- No unnecessary layers or indirection
- No premature optimization

### Architecture awareness

- Keep domain tests framework-independent
- Test use cases for orchestration correctness
- Keep infrastructure replaceable
- **Adapt to what exists** — never impose architecture the project doesn't use

---

## Test Naming

```
it('should return "0m" when milliseconds is negative')   // Good
it('should call presentFailure with ISSUE_NOT_FOUND when issue not found')  // Good
it('test negative case')                                   // Bad
it('works correctly')                                      // Bad
```

Describe condition + expectation in every test name.

---

## Forbidden Patterns

- **Never skip layers** — implement bottom-up: domain → ports → application → infrastructure → DI → UI
- **Never mock domain logic** — domain is pure; test with direct input/output
- **Never hardcode conventions** — always discover from project documentation
- **Never import across architecture boundaries** — follow each layer's allowed imports
- **Never commit code without verification** — run lint + type-check + tests after every phase

---

## Relationship to Other Skills

| Skill | Relationship |
|-------|-------------|
| `bdd` | `feature-lifecycle` uses the same spec format and test-first philosophy, but orchestrates across all architecture layers. Use `bdd` for a single unit; use `feature-lifecycle` for a full feature |
| `test-writer` | Use `test-writer` for adding tests to **existing** code. Use `feature-lifecycle` for driving **new** implementation |
| `component-scaffold` | `feature-lifecycle` Phase 7 may delegate individual component creation to `component-scaffold` |
| `code-review` | Run `code-review` after `feature-lifecycle` completes to verify the full feature before committing |

---

## Integration Checklist

After all phases complete, verify:

- [ ] All spec files exist and are reviewed
- [ ] Domain layer has no framework imports
- [ ] Port interfaces match what use cases expect
- [ ] Use case tests cover success + failure paths
- [ ] Infrastructure adapters implement all port methods
- [ ] DI module is registered and resolvable
- [ ] UI page renders without errors (if Phase 7 was active)
- [ ] All tests pass
- [ ] Lint reports zero errors
- [ ] Type-check passes
- [ ] No layer boundary violations (verify import rules)
