---
name: performance-review
description: Use to identify scalability and performance risks in a feature. Depth varies by caller: lightweight at planning time, deep when structural changes are involved, targeted at slice completion, comprehensive at pre-release gate.
---

## Purpose

Identify performance and scalability risks before they reach production. Catch expensive operations, unbounded processing, and inefficient patterns early when they are cheapest to fix.

---

## Invocation Depth

| Caller | Depth | Focus |
| ------ | ----- | ----- |
| feature-lifecycle-agent (planning) | Lightweight | Flag obvious N+1 risks, unbounded queries |
| architecture-agent (structural change) | Deep | Scalability impact, data access patterns, caching |
| verification-agent (slice completion) | Targeted | Specific slice's hot paths |
| delivery-agent (pre-release) | Comprehensive | Full feature performance audit |

---

## What to Review

### Data Access

| Pattern | Risk | Action |
| ------- | ---- | ------ |
| N+1 queries | Queries inside loops | Flag for batching or eager loading |
| Unbounded queries | No limit/pagination | Require explicit limits |
| Missing indexes | Filtering on non-indexed fields | Flag for index creation |
| Hydrating full records | Fetching all fields when only a subset is needed | Flag for projection |

### Processing

| Pattern | Risk | Action |
| ------- | ---- | ------ |
| Unbounded loops | Loop over user-supplied data with no size constraint | Require size limit |
| Synchronous blocking | Heavy computation on the request thread | Evaluate for async or background job |
| Redundant computation | Same value computed multiple times in a request | Flag for memoization or caching |

### Network

| Pattern | Risk | Action |
| ------- | ---- | ------ |
| Chatty APIs | Multiple sequential requests where one would do | Flag for batching |
| Large payloads | Returning entire collections without pagination | Require pagination |
| Missing caching | Frequently read, rarely changed data fetched every time | Evaluate caching layer |

### Rendering (UI)

| Pattern | Risk | Action |
| ------- | ---- | ------ |
| Rendering large lists without virtualization | DOM size scales with data | Flag for virtualization |
| Unnecessary re-renders | Components re-rendering without data change | Flag for memoization |
| Blocking renders | Synchronous data fetching before render | Flag for async with loading state |

---

## Findings Format

```markdown
# Performance Review: [Feature/Slice Name]

## Data Access
- [Severity] [Location]: [pattern] — [risk] — [recommendation]

## Processing
- [Severity] [Location]: [pattern] — [risk] — [recommendation]

## Network
- [Severity] [Location]: [pattern] — [risk] — [recommendation]

## Rendering
- [Severity] [Location]: [pattern] — [risk] — [recommendation]

## Overall Assessment
Status: CLEAN | RISKS FOUND
Risk level: LOW | MEDIUM | HIGH | CRITICAL
Recommended action: [proceed / fix before implementing / escalate]
```

---

## Severity Guide

| Severity | Definition |
| -------- | ---------- |
| Critical | Will fail under expected production load |
| High | Will degrade significantly under moderate load |
| Medium | Performance concern under high load or large data sets |
| Low | Minor inefficiency, acceptable to defer |

---

## Rules

- Do not flag theoretical risks that are not observable in the code
- Do not optimize prematurely — only flag patterns with clear, demonstrable risk
- A critical finding must be resolved before the feature ships
- A high finding must be resolved or explicitly accepted with a mitigation note
- Medium and low findings may be deferred with tracking
