# AI Engineering System Specification

## Philosophy

This system separates:

* **Agents** → decision makers and orchestrators
* **Skills** → specialized execution capabilities

Agents own objectives and workflow decisions.
Skills own implementation procedures and bounded expertise.

Skills are intentionally composable and reusable.
Agents coordinate them to achieve delivery outcomes safely and predictably.

---

# Directory Structure

```text
./agents/
 ├── feature-lifecycle-agent
 ├── architecture-agent
 ├── verification-agent
 └── delivery-agent

./skills/
 ├── requirement-clarification
 ├── acceptance-criteria
 ├── vertical-slice-planning
 ├── implementation
 ├── tdd
 ├── verification
 ├── architecture-review
 ├── performance-review
 ├── security-review
 ├── release-checklist
 └── observability-review
```

---

# AGENTS

---

# feature-lifecycle-agent

## Purpose

Primary software delivery orchestrator.

Transforms feature requests into:

* executable implementation flow
* validated delivery slices
* production-ready outcomes

This agent coordinates the entire engineering lifecycle from discovery to verified completion.

---

## Responsibilities

* Understand feature scope
* Resolve ambiguity
* Select execution strategy
* Sequence implementation
* Coordinate implementation and verification
* Enforce architectural constraints
* Ensure production readiness

---

## Non-Responsibilities

* Writing implementation details directly
* Deep architecture redesign
* Independent product strategy decisions

---

## Decision Authority

Can decide:

* execution order
* slicing strategy
* verification strictness
* implementation routing
* escalation to specialist agents

Cannot decide:

* business priorities
* organization-wide architecture redesign

---

## Consumed Skills

| Skill                     | Purpose                        |
| ------------------------- | ------------------------------ |
| requirement-clarification | Resolve ambiguity              |
| acceptance-criteria       | Define completion expectations |
| vertical-slice-planning   | Decompose feature safely       |
| implementation            | Execute slices                 |
| tdd                       | Author failing tests before implementation starts |
| verification              | Validate slice correctness     |
| architecture-review       | Validate boundaries            |
| performance-review        | Detect performance risks       |
| security-review           | Detect security risks          |
| observability-review      | Ensure monitoring readiness    |

---

## Delegates To

| Agent              | Reason                                      |
| ------------------ | ------------------------------------------- |
| architecture-agent | Complex architectural risk                  |
| verification-agent | High-risk verification orchestration        |
| delivery-agent     | Release readiness and deployment validation |

---

## Recovery Protocol

When `verification-agent` returns a failed verification escalation:

1. Classify the failure:
   - Implementation error → retry the slice via `implementation`
   - Acceptance criteria gap → return to `requirement-clarification`
   - Architectural violation → delegate to `architecture-agent`
2. Allow up to two retry attempts per slice
3. On third failure, escalate to human with a structured failure report including: slice-id, failure classification, findings, and retry history

---

## Outputs

* execution plan
* implementation sequence
* delivery summary
* escalation requests
* verification status
* failure reports

---

# architecture-agent

## Purpose

Protects long-term structural integrity of the system.

Ensures features preserve:

* architecture boundaries
* dependency direction
* modularity
* maintainability
* scalability

---

## Responsibilities

* Review architecture impact
* Detect coupling violations
* Detect boundary leaks
* Review dependency graph impact
* Validate module ownership
* Evaluate scalability implications

---

## Non-Responsibilities

* Product decisions
* Business prioritization
* Feature implementation

---

## Consumed Skills

| Skill                | Purpose                          |
| -------------------- | -------------------------------- |
| architecture-review  | Boundary and dependency analysis |
| performance-review   | Scalability impact review        |
| security-review      | Security boundary analysis       |
| observability-review | Operational visibility review    |

---

## Outputs

* architecture assessment
* violation reports
* mitigation recommendations
* refactor recommendations

---

# verification-agent

## Purpose

Ensures correctness and regression safety before delivery.

Acts as the system's verification authority.

---

## Responsibilities

* Coordinate validation flow
* Enforce verification completeness
* Detect regression risks
* Evaluate integration correctness
* Validate acceptance criteria fulfillment

---

## Consumed Skills

| Skill               | Purpose                       |
| ------------------- | ----------------------------- |
| verification        | Core verification execution   |
| tdd                 | Execute tests and confirm behavioral correctness |
| acceptance-criteria | Completion validation         |
| performance-review  | Performance regression checks |
| security-review     | Security validation           |

---

## Outputs

* verification reports
* regression summaries
* validation status
* failed verification escalation

---

# delivery-agent

## Purpose

Ensures the feature is operationally deliverable.

Focuses on:

* deployment safety
* release readiness
* production operability
* rollback preparedness

---

## Responsibilities

* Validate release readiness
* Validate operational visibility
* Validate rollback strategy
* Validate deployment safety
* Ensure production concerns are addressed

---

## Consumed Skills

| Skill                | Purpose                        |
| -------------------- | ------------------------------ |
| release-checklist    | Release validation             |
| observability-review | Monitoring readiness           |
| verification         | Final delivery validation      |
| performance-review   | Production performance safety  |
| security-review      | Production security validation |

---

## Outputs

* release readiness report
* deployment checklist
* rollback recommendations
* operational readiness assessment

---

# SKILLS

---

# requirement-clarification

## Purpose

Resolve ambiguity before planning or implementation.

---

## Responsibilities

* Detect missing requirements
* Identify conflicting requirements
* Ask batched clarification questions
* Apply sensible defaults where appropriate
* Surface hidden assumptions

---

## Inputs

* feature request
* partial specifications
* user intent

---

## Outputs

* clarified requirements
* assumptions
* unresolved questions
* defaulted decisions

---

## Used By

* feature-lifecycle-agent

---

# acceptance-criteria

## Purpose

Define measurable completion conditions.

---

## Responsibilities

* Convert requirements into verifiable outcomes
* Define behavioral expectations
* Define done conditions
* Identify edge cases

---

## Outputs

* acceptance checklist
* behavioral expectations
* validation targets

---

## Used By

* feature-lifecycle-agent
* verification-agent

---

# vertical-slice-planning

## Purpose

Decompose features into independently deliverable slices.

---

## Responsibilities

* Create vertical slices
* Minimize context complexity
* Sequence dependencies
* Optimize implementation flow

---

## Outputs

* slice definitions
* execution sequence
* dependency graph

---

## Used By

* feature-lifecycle-agent

---

# implementation

## Purpose

Execute implementation tasks safely and consistently.

---

## Responsibilities

* Use TDD output as implementation guide — tests are written before code
* Implement feature logic against failing tests
* Validate each change against the slice's acceptance criteria before marking done
* Respect architecture findings from `architecture-review` as hard constraints
* Follow project conventions and preserve code consistency
* Surface blockers to the orchestrating agent rather than silently continuing

---

## Outputs

* implementation changes
* code updates
* integration wiring

---

## Used By

* feature-lifecycle-agent

---

# tdd

## Purpose

Drive implementation through behavioral verification.

---

## Responsibilities

* Generate test scenarios
* Define failing-first behavior
* Validate edge cases
* Ensure behavioral correctness

---

## Outputs

* unit tests
* integration tests
* behavioral specifications

---

## Used By

| Agent                   | Invocation intent                                      |
| ----------------------- | ------------------------------------------------------ |
| feature-lifecycle-agent | Author failing tests before implementation begins      |
| verification-agent      | Execute tests and confirm behavioral correctness       |

---

# verification

## Purpose

Validate correctness and regression safety.

---

## Responsibilities

* Run validation procedures
* Detect regressions
* Validate integration flow
* Confirm acceptance criteria

---

## Outputs

* verification report
* regression analysis
* validation status

---

## Used By

* feature-lifecycle-agent
* verification-agent
* delivery-agent

---

# architecture-review

## Purpose

Protect architecture integrity.

---

## Responsibilities

* Detect boundary violations
* Detect circular dependencies
* Validate layering
* Evaluate coupling

---

## Outputs

* architecture findings
* risk assessment
* boundary analysis

---

## Used By

* feature-lifecycle-agent
* architecture-agent

---

# performance-review

## Purpose

Identify scalability and performance risks.

---

## Responsibilities

* Detect expensive operations
* Detect unbounded processing
* Detect rendering inefficiencies
* Detect network inefficiencies

---

## Outputs

* performance findings
* optimization recommendations
* scalability concerns

---

## Used By

| Agent                   | When invoked                                | Depth         |
| ----------------------- | ------------------------------------------- | ------------- |
| feature-lifecycle-agent | At planning time, per slice                 | Lightweight   |
| architecture-agent      | When structural changes are detected        | Deep          |
| verification-agent      | At slice completion                         | Targeted      |
| delivery-agent          | Final gate before release                   | Comprehensive |

---

# security-review

## Purpose

Identify security risks before release.

---

## Responsibilities

* Detect insecure flows
* Detect permission gaps
* Detect unsafe data handling
* Detect exposure risks

---

## Outputs

* security findings
* mitigation recommendations
* risk classification

---

## Used By

| Agent                   | When invoked                                | Depth         |
| ----------------------- | ------------------------------------------- | ------------- |
| feature-lifecycle-agent | At planning time, per slice                 | Lightweight   |
| architecture-agent      | When security boundaries are impacted       | Deep          |
| verification-agent      | At slice completion                         | Targeted      |
| delivery-agent          | Final gate before release                   | Comprehensive |

---

# release-checklist

## Purpose

Ensure deployment readiness.

---

## Responsibilities

* Validate deployment prerequisites
* Validate migration readiness
* Validate rollback preparation
* Validate release safety

---

## Outputs

* release checklist
* deployment blockers
* rollout recommendations

---

## Used By

* delivery-agent

---

# observability-review

## Purpose

Ensure operational visibility after release.

---

## Responsibilities

* Validate logging strategy
* Validate metrics coverage
* Validate tracing coverage
* Validate monitoring readiness

---

## Outputs

* observability assessment
* monitoring gaps
* instrumentation recommendations

---

## Used By

* feature-lifecycle-agent
* architecture-agent
* delivery-agent

---

# SYSTEM RELATIONSHIP MODEL

```text
FeatureLifecycleAgent
 ├── requirement-clarification
 ├── acceptance-criteria
 ├── vertical-slice-planning
 ├── implementation
 ├── tdd
 ├── verification
 ├── architecture-review
 ├── performance-review
 ├── security-review
 └── observability-review

ArchitectureAgent
 ├── architecture-review
 ├── performance-review
 ├── security-review
 └── observability-review

VerificationAgent
 ├── verification
 ├── tdd
 ├── acceptance-criteria
 ├── performance-review
 └── security-review

DeliveryAgent
 ├── release-checklist
 ├── observability-review
 ├── verification
 ├── performance-review
 └── security-review
```

---

# Agent Handoff Context

All agents consume and produce a shared context object. Each agent reads the full context and mutates only the fields it owns.

| Field                | Owner                    | Description                                              |
| -------------------- | ------------------------ | -------------------------------------------------------- |
| `feature-id`         | feature-lifecycle-agent  | Unique identifier for the feature                        |
| `requirements`       | feature-lifecycle-agent  | Clarified requirements output                            |
| `acceptance-criteria`| feature-lifecycle-agent  | Defined done conditions per slice                        |
| `slice-state`        | feature-lifecycle-agent  | List of slices with status: pending / in-progress / complete / failed |
| `findings[]`         | architecture/verification-agent | Accumulated architecture, security, and performance findings |
| `status`             | any agent                | active \| blocked \| escalated \| complete               |

Agents pass this context forward unchanged except for their owned fields. Mutations outside owned fields require explicit escalation.

---

# Future Evolution

Potential future agents and their introduction triggers:

| Agent            | Introduce when                                                      |
| ---------------- | ------------------------------------------------------------------- |
| frontend-agent   | UI concerns appear in the majority of feature slices                |
| backend-agent    | API/data-layer concerns dominate and need isolated orchestration    |
| infra-agent      | Infrastructure changes are required as part of feature delivery     |
| migration-agent  | Features regularly require schema or data migrations                |
| incident-agent   | Post-incident remediation becomes a recurring delivery workflow     |
| refactor-agent   | Structural debt is blocking feature velocity across multiple cycles |

Potential future skills and their introduction triggers:

| Skill                 | Introduce when                                                         |
| --------------------- | ---------------------------------------------------------------------- |
| api-contract-review   | Multiple consumers depend on the same API surface                      |
| migration-planning    | Features require schema changes with rollback complexity               |
| dependency-analysis   | Dependency graph violations or upgrade failures appear repeatedly      |
| accessibility-review  | UI work becomes a regular delivery concern                             |
| rollback-planning     | Release failures require structured rollback more than once            |
| contract-testing      | Cross-service integration failures occur in production                 |
| feature-flag-strategy | Incremental rollout becomes a standard delivery requirement            |

---

# Design Principles

## Agents Think

Agents:

* make decisions
* choose workflows
* orchestrate execution
* evaluate outcomes

---

## Skills Execute

Skills:

* perform bounded tasks
* remain composable
* stay reusable
* avoid orchestration responsibilities

---

## Preserve Context Efficiency

Keep skills narrow enough to:

* reduce context pollution
* improve reasoning consistency
* maximize execution reliability

---

## Skill Versioning

Skills evolve. To prevent silent breakage:

* Breaking changes to a skill's responsibilities require a new named variant (e.g., `tdd-strict`)
* Non-breaking additions (new outputs, refined steps) can be made in place
* Agents reference skills by name — if a variant is introduced, update the agent's consumed skills table explicitly

---

## Prefer Workflow Composition Over Prompt Monoliths

Avoid giant universal prompts.

Prefer:

* small reusable skills
* orchestrated execution
* explicit responsibility boundaries

Because eventually every giant AI prompt becomes:

```text
"enterprise middleware but in markdown"
```

Humanity continues its sacred tradition of reinventing distributed systems with worse debugging.
