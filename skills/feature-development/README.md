# Feature Development System

A composable agent + skill system for delivering software features end-to-end — from raw requirements to production-ready, verified code.

---

## How to Use

**Start here:** activate the `feature-lifecycle-agent`.

```
Use the feature-lifecycle-agent to build [your feature request].
```

The agent handles everything from there: clarifying requirements, planning slices, writing tests first, implementing, verifying, and handing off to delivery.

You can also target a specific agent or skill directly if you need a focused capability (see below).

---

## System Overview

The system separates two concerns:

| Type | Role | Lives in |
| ---- | ---- | -------- |
| **Agent** | Orchestrates — makes decisions, sequences work, delegates | `agents/` |
| **Skill** | Executes — performs a bounded, reusable task | `<skill-name>/SKILL.md` |

Agents think. Skills execute.

---

## Agents

### `feature-lifecycle-agent` ← start here

The primary delivery orchestrator. Takes a feature from raw request to verified, production-ready code.

**Workflow it runs:**

```
1. requirement-clarification   Resolve ambiguity before anything starts
2. acceptance-criteria         Define testable done conditions per behavior
3. vertical-slice-planning     Decompose into independently deliverable slices
4. [per slice]
   ├── architecture-review     Lightweight boundary check
   ├── performance-review      Lightweight performance check
   ├── security-review         Lightweight security check
   ├── tdd                     Author failing tests first
   ├── implementation          Write minimum code to pass tests
   └── verification            Lint → type-check → tests → criteria coverage
5. observability-review        Confirm the feature is instrumented
6. → delivery-agent            Final production readiness gate
```

If verification fails, it classifies the failure, retries up to twice, then escalates to you on the third failure with a structured report.

---

### `architecture-agent`

Invoked by `feature-lifecycle-agent` when complex architectural risk is detected — circular dependencies, boundary violations, or structural concerns that need deep analysis.

**Runs:** deep `architecture-review` + `performance-review` + `security-review` + `observability-review`

**Returns:** architecture assessment with CLEARED / BLOCKED status.

---

### `verification-agent`

Invoked by `feature-lifecycle-agent` for high-risk or multi-slice verification — when standard per-slice checks are not sufficient.

**Runs:** `tdd` (execution) + `verification` + cross-slice integration check + targeted `performance-review` + `security-review`

**Returns:** full verification report with PASS / FAIL / PARTIAL status.

---

### `delivery-agent`

The final gate before deployment. Invoked after all slices are verified.

**Runs:** final `verification` + `observability-review` + comprehensive `performance-review` + comprehensive `security-review` + `release-checklist`

**Returns:** release readiness report with READY TO SHIP / BLOCKED status.

---

## Skills

Skills are reusable, bounded capabilities that agents invoke. You can also activate them directly for focused tasks.

| Skill | What it does | Use directly when... |
| ----- | ------------ | -------------------- |
| `requirement-clarification` | Resolves ambiguity, batches clarifying questions | You have a vague spec and want to extract clean requirements |
| `acceptance-criteria` | Converts requirements into Given/When/Then criteria | You need a testable definition of done for a feature |
| `vertical-slice-planning` | Decomposes features into independently deliverable slices | You need to plan implementation order |
| `tdd` | Authors failing tests before implementation; or executes and validates tests | You want test-first authoring, or need to validate behavioral coverage |
| `implementation` | Implements minimum code guided by failing tests and acceptance criteria | You have tests and need implementation |
| `verification` | Runs lint → type-check → tests; detects regressions; validates criteria | You want to validate a completed slice or feature |
| `architecture-review` | Detects boundary violations, circular deps, coupling issues | You want to audit a feature's structural integrity |
| `performance-review` | Identifies N+1, unbounded queries, rendering inefficiencies | You want to assess a feature for performance risk |
| `security-review` | Detects auth gaps, injection risks, data exposure | You want a security audit of a feature |
| `observability-review` | Validates logging, metrics, tracing, alerting | You want to confirm a feature is production-observable |
| `release-checklist` | Gates deployment on migrations, rollback, config, monitoring | You need a pre-release validation checklist |

---

## Workflow Diagram

```
User: "Build [feature]"
        │
        ▼
feature-lifecycle-agent
        │
        ├── requirement-clarification
        ├── acceptance-criteria
        ├── vertical-slice-planning
        │
        ├── [for each slice]
        │     ├── architecture-review (lightweight)
        │     ├── performance-review  (lightweight)
        │     ├── security-review     (lightweight)
        │     ├── tdd (author)
        │     ├── implementation
        │     └── verification
        │           │
        │           └── [on failure] ──► recovery protocol
        │                                     │
        │                          ┌──────────┼──────────────┐
        │                          ▼          ▼              ▼
        │                    implementation  requirement-  architecture-agent
        │                    (retry)         clarification
        │
        ├── observability-review
        │
        └── delivery-agent
                │
                ├── verification (final)
                ├── observability-review (comprehensive)
                ├── performance-review  (comprehensive)
                ├── security-review     (comprehensive)
                └── release-checklist
                        │
                        ▼
                READY TO SHIP / BLOCKED
```

---

## Review Depth

`performance-review` and `security-review` are invoked at different depths depending on the calling agent:

| Agent | Depth | When |
| ----- | ----- | ---- |
| `feature-lifecycle-agent` | Lightweight | At planning time, before each slice |
| `architecture-agent` | Deep | When structural changes are involved |
| `verification-agent` | Targeted | At slice completion |
| `delivery-agent` | Comprehensive | Final gate before release |

---

## Directory Structure

```
skills/feature-development/
├── README.md                          ← you are here
├── spec.md                            ← system design specification
├── agents/
│   ├── feature-lifecycle-agent.md    ← start here
│   ├── architecture-agent.md
│   ├── verification-agent.md
│   └── delivery-agent.md
├── requirement-clarification/SKILL.md
├── acceptance-criteria/SKILL.md
├── vertical-slice-planning/SKILL.md
├── implementation/SKILL.md
├── tdd/SKILL.md
├── verification/SKILL.md
├── architecture-review/SKILL.md
├── performance-review/SKILL.md
├── security-review/SKILL.md
├── observability-review/SKILL.md
└── release-checklist/SKILL.md
```
