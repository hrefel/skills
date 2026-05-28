---
name: architecture-review
description: Use to validate that a feature preserves architecture boundaries, avoids coupling violations, and respects layer dependency rules. Run at planning time (lightweight) or when structural changes are detected (deep).
---

## Purpose

Protect the long-term structural integrity of the system. Detect boundary violations, circular dependencies, and coupling issues before they compound into structural debt.

---

## Invocation Depth

Depth is determined by the calling agent:

| Caller | Depth | Focus |
| ------ | ----- | ----- |
| feature-lifecycle-agent (planning) | Lightweight | Flag obvious boundary risks |
| architecture-agent (structural change) | Deep | Full boundary, coupling, dependency analysis |
| verification-agent (slice completion) | Targeted | Verify the slice's imports and exports |
| delivery-agent (pre-release) | Comprehensive | Full cross-slice architecture integrity |

---

## What to Review

### Layer Boundaries

Verify that each layer only imports from permitted layers:

| Layer | May import | Must not import |
| ----- | ---------- | --------------- |
| Domain | other domain types | application, infrastructure, UI, frameworks |
| Application | domain, ports | infrastructure adapters, UI |
| Infrastructure | ports, external libraries | domain business logic |
| UI | application hooks/stores, UI primitives | infrastructure, domain directly |

Flag any violation as a **boundary breach** — severity: high.

### Circular Dependencies

Check for circular import chains. Example: A imports B, B imports C, C imports A.

Tools to check:
- TypeScript: `madge --circular src/`
- Manual: trace import chains for suspicious patterns
- Flag any cycle as a **circular dependency** — severity: critical.

### Coupling Analysis

Evaluate whether modules are overly coupled:

- Does a domain entity know about infrastructure details?
- Does an application use case depend on a specific adapter implementation (not its port)?
- Does the UI access the domain layer directly?

Flag coupling violations with the specific import path.

### Dependency Direction

Dependency flow must always point inward (toward domain):

```
UI → Application → Domain
Infrastructure → Ports (Application) → Domain
```

Any dependency pointing outward (domain importing application, etc.) is a **direction violation** — severity: high.

### Module Ownership

Verify that each feature's types, services, and adapters are owned by the correct module:
- No feature should reach into another feature's internals
- Shared types belong in a shared/common module, not within a feature

---

## Findings Format

```markdown
# Architecture Review: [Feature/Slice Name]

## Boundary Violations
- [Severity] [File] imports [Forbidden file]: [description]

## Circular Dependencies
- [Cycle path]: [A → B → C → A]

## Coupling Issues
- [File]: [description of coupling problem]

## Dependency Direction Violations
- [File]: [description]

## Overall Assessment
Status: CLEAN | VIOLATIONS FOUND
Risk level: LOW | MEDIUM | HIGH | CRITICAL
Recommended action: [proceed / fix before implementing / escalate to architecture-agent]
```

---

## Rules

- A critical violation (circular dependency) must be fixed before implementation proceeds
- A high violation (boundary breach) must be resolved or explicitly accepted with a mitigation plan
- A medium violation should be flagged and tracked as technical debt
- Do not invent violations — only flag what is directly observable from imports and structure
- When in doubt about a pattern, escalate to architecture-agent rather than guessing
