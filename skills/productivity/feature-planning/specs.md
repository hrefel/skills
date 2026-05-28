# Feature Planning Skill Specification

## Overview

`feature-planning` is an engineering-oriented planning skill responsible for transforming feature requests into structured, implementable technical execution plans.

This skill focuses on:
- requirement clarification
- feature decomposition
- implementation sequencing
- technical boundary identification
- verification planning

This is **not** a business analyst role and **not** an implementation skill.

Its purpose is to reduce ambiguity and prepare implementation-ready execution slices for downstream skills or agents.

---

# Responsibilities

## 1. Requirement Clarification

Transform ambiguous requests into clear technical requirements.

### Example

#### Input
```text
User wants Google Login
```

#### Output
```text
- OAuth flow required
- token persistence strategy
- callback handling
- session expiration handling
- loading and error states
- backend contract requirements
```

---

## 2. Feature Decomposition

Break large features into smaller implementation slices.

### Example

```text
Checkout Feature
├── cart validation
├── shipping selection
├── payment method
├── order summary
└── payment submission
```

The decomposition should:
- reduce implementation complexity
- minimize context size
- improve testability
- enable incremental delivery

---

## 3. Technical Boundary Identification

Identify:
- affected modules
- domain boundaries
- infrastructure impact
- API contract changes
- shared state impact
- dependency impact

---

## 4. Risk Identification

Detect potential technical risks such as:
- race conditions
- authentication impact
- state synchronization issues
- migration requirements
- backward compatibility risks
- architectural violations

---

## 5. Verification Planning

Define:
- required test coverage
- acceptance criteria
- integration validation points
- regression risk areas
- verification strategy

---

## 6. Implementation Sequencing

Define the recommended implementation order.

### Example

```text
1. Domain model
2. Contracts/interfaces
3. Infrastructure layer
4. UI integration
5. Verification/testing
```

Or use vertical slice sequencing when appropriate.

---

# Inputs

```yaml
inputs:
  - feature_request
  - existing_architecture
  - project_constraints
  - coding_guidelines
  - technical_requirements
```

---

# Outputs

```yaml
outputs:
  - clarified_requirements
  - implementation_slices
  - affected_modules
  - technical_risks
  - acceptance_criteria
  - verification_plan
  - implementation_sequence
```

---

# Principles

## Prefer Vertical Slices

Avoid large horizontal implementation phases when possible.

Prefer:
```text
feature → complete slice → verify → continue
```

Instead of:
```text
all backend → all frontend → all tests
```

---

## Preserve Architecture Boundaries

Do not introduce coupling that violates the existing architecture.

Respect:
- clean architecture boundaries
- dependency direction
- modular separation
- domain ownership

---

## Optimize for Testability

Implementation slices should:
- be independently testable
- have clear verification points
- minimize hidden side effects

---

## Reduce Context Complexity

Plans should:
- minimize implementation scope per step
- reduce cognitive overload
- improve execution predictability

---

# Non-Responsibilities

This skill must NOT:
- write production code
- modify files
- execute tests
- redesign the entire architecture
- perform refactoring directly
- make product/business decisions

---

# Recommended Workflow Position

```text
feature-request
        ↓
feature-planning
        ↓
implementation skill / sub-agent
        ↓
verification
        ↓
review
```

---

# Recommended Future Composition

```text
feature-lifecycle/
├── feature-planning/
├── implementation/
├── verification/
├── architecture-review/
├── tdd-enforcement/
└── completion/
```

Where:
- smaller skills remain composable
- orchestration remains maintainable
- responsibilities stay isolated

---

# Anti-Patterns

## Avoid God Planner Behavior

Do not combine:
- planning
- implementation
- debugging
- reviewing
- architecture redesign

into a single skill.

Large multi-purpose planning skills tend to create:
- instruction conflicts
- unstable outputs
- context pollution
- inconsistent execution

---

# Success Criteria

A successful plan should:
- reduce ambiguity
- produce implementation-ready slices
- identify risks early
- preserve architecture integrity
- improve implementation predictability
- simplify downstream execution