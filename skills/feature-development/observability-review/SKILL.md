---
name: observability-review
description: Use to ensure a feature has adequate operational visibility after release. Validates logging, metrics, tracing, and monitoring readiness. Run at planning time and before release.
---

## Purpose

Ensure that once the feature is live, the team can observe its behavior, diagnose problems, and respond to incidents without flying blind.

---

## What to Review

### Logging

| Check | Good | Bad |
| ----- | ---- | --- |
| Structured logs | `{ event: "order.created", orderId, userId }` | `"Order created for user 123"` |
| Log levels used correctly | ERROR for failures, INFO for significant events, DEBUG for detail | All logs at INFO or all at DEBUG |
| PII excluded | Logs contain IDs, not raw sensitive data | Email, password, credit card in logs |
| Key operations logged | Entry points, external calls, failures all logged | Silent success paths with no log |
| Errors logged with context | Error message + relevant IDs + stack trace | Bare `console.error(e)` |

### Metrics

| Check | What to verify |
| ----- | -------------- |
| Request/operation count | Are key operations counted? |
| Error rate | Are failures tracked and distinguishable from successes? |
| Latency | Are slow paths measurable? |
| Business metrics | Are domain-significant events measured (orders created, payments processed)? |

### Distributed Tracing

| Check | What to verify |
| ----- | -------------- |
| Span coverage | Does a trace span cover the feature's entry points? |
| Context propagation | Is trace context passed across service boundaries? |
| Span attributes | Do spans include enough context (feature name, resource IDs) to be useful? |

### Alerting

| Check | What to verify |
| ----- | -------------- |
| Error rate alert | Is there an alert if error rate exceeds threshold? |
| Latency alert | Is there an alert if p95 latency exceeds threshold? |
| Business metric alert | Is there an alert if a critical business metric drops unexpectedly? |
| On-call runbook | Does an alert link to a runbook that describes how to respond? |

---

## Findings Format

```markdown
# Observability Review: [Feature/Slice Name]

## Logging
- [Severity] [Location]: [gap] — [recommendation]

## Metrics
- [Severity] [Location]: [gap] — [recommendation]

## Tracing
- [Severity] [Location]: [gap] — [recommendation]

## Alerting
- [Severity] [Location]: [gap] — [recommendation]

## Overall Assessment
Status: READY | GAPS FOUND
Risk level: LOW | MEDIUM | HIGH
Recommended action: [proceed / add instrumentation / escalate]
```

---

## Severity Guide

| Severity | Definition |
| -------- | ---------- |
| High | The feature would be undiagnosable in production without this |
| Medium | Diagnosing incidents would be significantly harder without this |
| Low | Nice to have; improves operational experience |

---

## Rules

- High gaps must be resolved before release — a feature that cannot be diagnosed is not production-ready
- Observability is not an afterthought — instrument at implementation time, not after an incident
- Do not require exhaustive logging of every line — only key operations, failures, and entry points
- Prefer structured, machine-parseable logs over freeform text
