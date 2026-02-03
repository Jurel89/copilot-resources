# Issue Body Templates

Use these templates when creating issue bodies. Select the appropriate template based on issue type.

## Feature Request (FR-####)

```markdown
## Summary

[One-line description of the feature]

## User Story

As a [persona], I want [capability] so that [benefit].

## Motivation

[Why is this feature needed? What problem does it solve?]

## Proposed Solution

[How should this feature work?]

## Acceptance Criteria

- [ ] AC-001: [Specific, testable criterion]
- [ ] AC-002: [Specific, testable criterion]
- [ ] AC-003: [Specific, testable criterion]

## Technical Notes

[Implementation guidance, architectural considerations]

## Alternatives Considered

[Other approaches considered and why they weren't chosen]

## Dependencies

[Related requirements or external dependencies]

## Additional Context

[Mockups, examples, or related issues]
```

## Bug (BG-####)

```markdown
## Description

[Clear description of the bug]

## Steps to Reproduce

1. [First step]
2. [Second step]
3. [And so on...]

## Expected Behavior

[What should happen]

## Actual Behavior

[What actually happens]

## Environment

- Browser: [e.g., Chrome 120]
- OS: [e.g., macOS 14.0]
- Version: [e.g., v1.2.3]

## Acceptance Criteria

- [ ] AC-001: [Specific fix verification]
- [ ] AC-002: [Regression test added]

## Screenshots/Logs

[If applicable]

## Additional Context

[Any other relevant information]
```

## Hotfix (HF-####)

```markdown
## CRITICAL ISSUE

**Impact**: [Describe the severity and scope of impact]
**Affected Users**: [Who is impacted]
**Timeline**: [When did this start / urgency level]

## Description

[Clear description of the critical issue]

## Root Cause (if known)

[What caused this issue]

## Proposed Fix

[Immediate fix approach]

## Steps to Reproduce

1. [First step]
2. [Second step]

## Acceptance Criteria

- [ ] AC-001: [Issue is resolved]
- [ ] AC-002: [No regression in related functionality]
- [ ] AC-003: [Monitoring confirms fix]

## Rollback Plan

[If fix fails, how to rollback]

## Post-Mortem

- [ ] Schedule post-mortem after resolution
```

## Technical Chore (TC-####)

```markdown
## Objective

[What needs to be accomplished]

## Rationale

[Why this matters - tech debt impact, maintenance burden, etc.]

## Details

[Detailed description of the work]

## Checklist

- [ ] [Subtask 1]
- [ ] [Subtask 2]
- [ ] [Subtask 3]

## Acceptance Criteria

- [ ] AC-001: [Specific completion criterion]
- [ ] AC-002: [Tests pass]
- [ ] AC-003: [Documentation updated if needed]

## Dependencies

[Any blockers or related work]

## Notes

[Additional context or considerations]
```

## Security (SC-####)

```markdown
## Security Issue

**Severity**: [Critical / High / Medium / Low]
**CVE** (if applicable): [CVE-XXXX-XXXXX]
**CVSS Score** (if applicable): [X.X]

## Description

[Description of the security issue - be careful with sensitive details]

## Impact

[What could happen if exploited]

## Affected Components

- [Component 1]
- [Component 2]

## Remediation

[Proposed fix approach]

## Acceptance Criteria

- [ ] AC-001: [Vulnerability is mitigated]
- [ ] AC-002: [Security scan passes]
- [ ] AC-003: [No regression in functionality]

## Disclosure

- [ ] Follow responsible disclosure timeline
- [ ] Notify affected parties if required
```

## Performance (PF-####)

```markdown
## Performance Issue

**Current Metric**: [e.g., p95 latency 800ms]
**Target Metric**: [e.g., p95 latency <200ms]
**Affected Area**: [Component, endpoint, feature]

## Description

[Description of the performance issue]

## Analysis

[Root cause analysis, profiling results]

## Proposed Optimization

[Approach to improve performance]

## Acceptance Criteria

- [ ] AC-001: [Target metric achieved]
- [ ] AC-002: [No regression in other metrics]
- [ ] AC-003: [Load test validates improvement]

## Benchmarks

[Before/after comparison methodology]

## Notes

[Additional context]
```

## Documentation (DC-####)

```markdown
## Documentation Update

**Type**: [New / Update / Delete]
**Audience**: [Developers / Users / Operators]

## Summary

[What documentation is being added/updated]

## Motivation

[Why this documentation is needed]

## Content Outline

1. [Section 1]
2. [Section 2]
3. [Section 3]

## Acceptance Criteria

- [ ] AC-001: [Documentation is accurate]
- [ ] AC-002: [Examples are working]
- [ ] AC-003: [Reviewed by subject matter expert]

## Related Resources

[Links to related docs, code, or issues]
```

## Infrastructure (IN-####)

```markdown
## Infrastructure Change

**Environment**: [Production / Staging / Development / All]
**Change Type**: [CI/CD / Deployment / Monitoring / Scaling]

## Summary

[What infrastructure change is needed]

## Motivation

[Why this change is needed]

## Implementation Plan

1. [Step 1]
2. [Step 2]
3. [Step 3]

## Acceptance Criteria

- [ ] AC-001: [Change is deployed successfully]
- [ ] AC-002: [Monitoring confirms health]
- [ ] AC-003: [Runbook updated if needed]

## Rollback Plan

[How to revert if issues arise]

## Dependencies

[Other systems or teams affected]

## Maintenance Window

[If downtime required, specify window]
```

## Minimal Template

For simple issues when full templates aren't needed:

```markdown
## Description

[What and why]

## Tasks

- [ ] [Task 1]
- [ ] [Task 2]

## Acceptance Criteria

- [ ] AC-001: [Completion criterion]
```
