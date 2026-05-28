---
name: security-review
description: Use to identify security risks in a feature before release. Depth varies by caller: lightweight at planning time, deep when security boundaries are impacted, targeted at slice completion, comprehensive at pre-release gate.
---

## Purpose

Identify security vulnerabilities and risks before they reach production. Catch insecure flows, permission gaps, and unsafe data handling early when they are cheapest to fix.

---

## Invocation Depth

| Caller | Depth | Focus |
| ------ | ----- | ----- |
| feature-lifecycle-agent (planning) | Lightweight | Flag obvious auth gaps, sensitive data handling |
| architecture-agent (security boundary change) | Deep | Full boundary analysis, trust model review |
| verification-agent (slice completion) | Targeted | Slice's specific security surface |
| delivery-agent (pre-release) | Comprehensive | Full feature security audit |

---

## What to Review

### Authentication and Authorization

| Check | Look for |
| ----- | -------- |
| Auth enforcement | Every protected endpoint/action checks authentication |
| Permission checks | Role and ownership checks are present and not bypassable |
| Privilege escalation | Users cannot access resources beyond their permission level |
| Auth bypass | Parameters that could skip auth checks |

### Input Validation

| Check | Look for |
| ----- | -------- |
| Injection risks | User input used in queries, commands, or templates without sanitization (SQL injection, command injection, template injection) |
| XSS | User-controlled data rendered as HTML without escaping |
| Path traversal | File paths constructed from user input |
| Schema validation | All incoming data validated against a strict schema before use |

### Data Handling

| Check | Look for |
| ----- | -------- |
| Sensitive data exposure | PII, credentials, or secrets logged or returned in responses unnecessarily |
| Insecure storage | Sensitive data stored in plaintext |
| Data leakage | API responses returning more data than the caller is authorized to see |
| Secrets in code | Hardcoded credentials, API keys, or tokens |

### Trust Boundaries

| Check | Look for |
| ----- | -------- |
| Server-side enforcement | Business rules enforced on the server, not just the client |
| CSRF protection | State-changing operations protected against cross-site request forgery |
| Rate limiting | Sensitive operations (login, password reset) protected against brute force |
| CORS policy | Cross-origin policy is intentional and restrictive |

---

## Findings Format

```markdown
# Security Review: [Feature/Slice Name]

## Authentication / Authorization
- [Severity] [Location]: [vulnerability] — [impact] — [recommendation]

## Input Validation
- [Severity] [Location]: [vulnerability] — [impact] — [recommendation]

## Data Handling
- [Severity] [Location]: [vulnerability] — [impact] — [recommendation]

## Trust Boundaries
- [Severity] [Location]: [vulnerability] — [impact] — [recommendation]

## Overall Assessment
Status: CLEAN | RISKS FOUND
Risk level: LOW | MEDIUM | HIGH | CRITICAL
Recommended action: [proceed / fix before implementing / escalate]
```

---

## Severity Guide

| Severity | Definition |
| -------- | ---------- |
| Critical | Directly exploitable vulnerability (auth bypass, injection, data breach) |
| High | Significant risk requiring a specific attack condition |
| Medium | Defense-in-depth gap or indirect risk |
| Low | Minor hardening opportunity |

---

## Rules

- A critical or high finding must be resolved before the feature ships — no exceptions
- Do not flag theoretical risks without observable evidence in the code
- Security review findings are not optional technical debt — they are blockers
- When in doubt about a risk classification, escalate to architecture-agent rather than downgrading
