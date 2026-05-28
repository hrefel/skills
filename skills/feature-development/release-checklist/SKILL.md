---
name: release-checklist
description: Use before releasing a feature to production. Validates deployment prerequisites, migration readiness, rollback preparation, and release safety. Invoked exclusively by delivery-agent.
---

## Purpose

Ensure the feature is safe to deploy. Block release on any unresolved prerequisite. The checklist is a gate — every item must be resolved or explicitly accepted before proceeding.

---

## Checklist

### Code Readiness

- [ ] All acceptance criteria are satisfied
- [ ] All tests pass (lint, type-check, unit, integration)
- [ ] No critical or high findings from architecture-review, performance-review, or security-review are unresolved
- [ ] No unresolved TODO or FIXME comments in the feature's code
- [ ] Code has been reviewed (if review process applies)

### Data and Migrations

- [ ] All required database migrations are written and tested
- [ ] Migrations are backwards-compatible with the current production schema (if zero-downtime deployment)
- [ ] Migration rollback scripts exist and have been verified
- [ ] Data backfill requirements (if any) are planned and staged separately from schema migrations

### Configuration and Environment

- [ ] All required environment variables are documented
- [ ] Environment variables are present in all target environments (staging, production)
- [ ] Feature flags (if used) are configured for the intended rollout scope
- [ ] Third-party service credentials and API keys are provisioned

### Observability

- [ ] Key operations are instrumented with structured logs
- [ ] Metrics for critical paths are in place
- [ ] Distributed tracing spans cover the feature's entry points
- [ ] Alerts for error rate or latency thresholds are configured (or a gap is explicitly accepted)

### Rollback

- [ ] A rollback plan exists: what steps reverse this deployment?
- [ ] The rollback plan has been reviewed and is executable without the original deployer
- [ ] Data migrations are reversible, or a data recovery procedure is documented
- [ ] Feature flags (if used) allow disabling the feature without a redeploy

### Deployment

- [ ] Deployment steps are documented
- [ ] Any required infrastructure changes are provisioned (queues, topics, storage buckets, etc.)
- [ ] Dependencies on other services or teams are confirmed ready
- [ ] Staged rollout plan (canary, percentage rollout) is defined if applicable

---

## Output Format

```markdown
# Release Checklist: [Feature Name]

## Status Summary
Blocking items: [count]
Non-blocking items: [count]
Overall: READY TO SHIP | BLOCKED

## Blocking Items
- [ ] [Item]: [what needs to be done]

## Accepted Risks
- [Item]: [why this is accepted and who accepted it]

## Release Notes (for on-call)
[Brief description of what changed, how to verify it's working, and how to roll back]
```

---

## Rules

- Every blocking item must be resolved before deployment — no "we'll fix it after"
- An item can be accepted (not blocked) only if a named person explicitly acknowledges the risk in writing
- The rollback plan must be executable by someone who was not involved in the feature
- Do not skip checklist items because the feature is small — every release carries risk
