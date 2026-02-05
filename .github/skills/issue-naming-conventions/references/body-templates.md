# Issue Body Templates

Select template by issue type prefix. Each includes required sections marked with *.

---

## FR: Feature Request

```markdown
## Summary*
[One-line description]

## User Story*
As a **[role]**, I want **[capability]**, so that **[benefit]**.

## Problem Statement
[Current state and pain points]

## Proposed Solution*
[How it should work]

## Acceptance Criteria*
- [ ] AC-001: [Testable criterion]
- [ ] AC-002: [Testable criterion]

## Technical Notes
[Architecture, API changes, dependencies]

## Out of Scope
[What this does NOT include]
```

---

## BG: Bug

```markdown
## Summary*
[One-line bug description]

**Severity**: P1/P2/P3 | **Frequency**: Always/Sometimes/Rarely | **Regression**: Yes/No

## Environment*
OS: | Browser: | Version: | User Role:

## Steps to Reproduce*
1. [Step]
2. [Step]
3. Observe [error]

## Expected vs Actual*
- **Expected**: [Correct behavior]
- **Actual**: [What happens]

## Error Details
```
[Error message/stack trace]
```

## Acceptance Criteria*
- [ ] AC-001: Bug no longer reproducible
- [ ] AC-002: Regression test added
```

---

## HF: Hotfix

```markdown
## 🚨 CRITICAL INCIDENT

| Severity | Status | Started | Impact |
|----------|--------|---------|--------|
| P0 | Investigating/Identified/Resolved | YYYY-MM-DD HH:MM UTC | [Who/what affected] |

## Problem*
[What is broken]

## Root Cause
[If known]

## Resolution*
[Fix approach]

## Rollback Plan*
1. [Rollback step]

## Acceptance Criteria*
- [ ] AC-001: Service restored
- [ ] AC-002: Error rate normal for 30min
- [ ] AC-003: Post-mortem scheduled
```

---

## TC: Technical Chore

```markdown
## Objective*
[What needs to be done]

## Rationale*
[Why this matters - tech debt impact]

## Scope
**In**: [Items] | **Out**: [Items]

## Tasks*
- [ ] [Task 1]
- [ ] [Task 2]

## Acceptance Criteria*
- [ ] AC-001: [Criterion]
- [ ] AC-002: Tests pass
- [ ] AC-003: No regression
```

---

## SC: Security

```markdown
## ⚠️ Security Issue

| Severity | CVE | CVSS | Disclosure |
|----------|-----|------|------------|
| Critical/High/Medium/Low | CVE-XXXX-XXXXX | X.X | Private/Public |

> ⚠️ Do NOT include exploit details in public issues.

## Summary*
[Brief description without revealing exploit]

## Impact*
[What's at risk - data, access, compliance]

## Remediation*
[Fix approach]

## Acceptance Criteria*
- [ ] AC-001: Vulnerability mitigated
- [ ] AC-002: Security scan passes
- [ ] AC-003: No regression
```

---

## PF: Performance

```markdown
## Performance Issue

| Metric | Current | Target |
|--------|---------|--------|
| [e.g., p95 latency] | [800ms] | [<200ms] |

**Affected**: [Component/endpoint]

## Problem*
[Current performance and user impact]

## Root Cause
[Bottleneck analysis]

## Proposed Fix*
[Optimization approach]

## Acceptance Criteria*
- [ ] AC-001: Target metric achieved
- [ ] AC-002: No regression in other metrics
- [ ] AC-003: Load test validates
```

---

## DC: Documentation

```markdown
## Documentation Update

**Type**: New/Update/Delete | **Audience**: Devs/Users/Ops

## Summary*
[What documentation is changing]

## Content Outline*
1. [Section]
2. [Section]

## Acceptance Criteria*
- [ ] AC-001: Technically accurate
- [ ] AC-002: Examples tested
- [ ] AC-003: Reviewed by SME
```

---

## IN: Infrastructure

```markdown
## Infrastructure Change

| Environment | Type | Risk | Downtime |
|-------------|------|------|----------|
| Prod/Staging/Dev | CI-CD/Deploy/Monitor | Low/Med/High | Yes/No |

## Summary*
[What infrastructure change]

## Implementation*
1. [Step]
2. [Step]

## Rollback Plan*
1. [Rollback step]

## Acceptance Criteria*
- [ ] AC-001: Change deployed
- [ ] AC-002: Health checks pass
- [ ] AC-003: Docs updated
```

---

## Minimal (Any Type)

```markdown
## Summary*
[What and why]

## Tasks
- [ ] [Task]

## Acceptance Criteria*
- [ ] AC-001: [Criterion]
```
