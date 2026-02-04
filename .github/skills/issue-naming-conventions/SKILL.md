---
name: issue-naming-conventions
description: 'Standards for GitHub issue naming and requirement IDs. Use when creating issues, assigning IDs (FR-####, BG-####, HF-####, TC-####, SC-####, PF-####, DC-####, IN-####), or ensuring issue traceability. Triggers on: issue naming, requirement ID, issue prefix, check existing issues, increment issue number, ID conventions.'
license: Complete terms in LICENSE.txt
---

# Issue Naming Conventions

This skill defines the standard conventions for naming GitHub issues and assigning requirement IDs to ensure traceability and consistency.

## When to Use This Skill

- Creating new GitHub issues with proper ID prefixes
- Determining the next sequential ID for a requirement type
- Checking existing issues before creating new ones
- Ensuring issue traceability across the codebase
- Any workflow involving standardized issue naming

## Requirement ID System

**CRITICAL**: Every issue MUST have a standardized, traceable ID with proper prefix and sequential numbering.

### Requirement Type Prefixes

| Prefix | Type | Description | Example |
| ------ | ---- | ----------- | ------- |
| `FR-####` | Feature Request | New features, enhancements, capabilities | `FR-0042` |
| `HF-####` | Hotfix | Urgent production fixes, critical bugs | `HF-0015` |
| `BG-####` | Bug | Non-critical bugs, defects, issues | `BG-0089` |
| `TC-####` | Technical Chore | Refactoring, tech debt, maintenance | `TC-0023` |
| `SC-####` | Security | Security fixes, vulnerabilities, compliance | `SC-0007` |
| `PF-####` | Performance | Performance improvements, optimizations | `PF-0011` |
| `DC-####` | Documentation | Documentation updates, guides, specs | `DC-0034` |
| `IN-####` | Infrastructure | CI/CD, deployment, infrastructure changes | `IN-0019` |

### ID Format Rules

1. **Always 4-digit zero-padded**: `FR-0001`, not `FR-1`
2. **Range**: 0001-9999 per prefix type
3. **Permanent allocation**: IDs are never reused, even for closed issues
4. **Title format**: `[PREFIX-####] Descriptive Title`

## ID Assignment Protocol

**MANDATORY WORKFLOW**: Before assigning ANY requirement ID:

### Step 1: Query Existing Issues

```bash
# Check all issues with a specific prefix
gh issue list --search "FR-" --state all --limit 200 --json number,title
```

### Step 2: Find the Highest Used Number

```bash
# Get the highest number for a prefix (example: FR)
gh issue list --search "FR-" --state all --limit 200 --json title --jq '.[].title' | grep -oE 'FR-[0-9]+' | sort -t'-' -k2 -n | tail -1
```

### Step 3: Increment Sequentially

- If highest is `FR-0042`, next is `FR-0043`
- If no issues exist with prefix, start at `0001`

### Step 4: Never Reuse IDs

Even if an issue is closed or deleted, its ID is permanently allocated.

## Issue Title Format

### Standard Format

```text
[PREFIX-####] Descriptive, actionable title
```

### Examples

| Type | Title |
| ---- | ----- |
| Feature | `[FR-0043] Add dark mode theme support` |
| Bug | `[BG-0089] Login fails when SSO is enabled` |
| Hotfix | `[HF-0015] Critical: API returns 500 on checkout` |
| Tech Chore | `[TC-0023] Refactor authentication module` |
| Security | `[SC-0007] Update dependencies with known CVEs` |
| Performance | `[PF-0011] Optimize database queries for dashboard` |
| Documentation | `[DC-0034] Add API reference for v2 endpoints` |
| Infrastructure | `[IN-0019] Configure staging environment CI/CD` |

### Title Guidelines

- Be specific and actionable
- Keep under 72 characters (including prefix)
- Use imperative mood ("Add", "Fix", "Update", not "Added", "Fixed")
- Include key context (component, scope)

## Issue Body Structure

### Standard Sections

Every issue should include:

```markdown
#### PREFIX-####: [Descriptive Requirement Title]
**Type**: [Feature Request | Bug | Hotfix | Technical Chore | Security | Performance | Documentation | Infrastructure]
**Priority**: P0/P1/P2/P3
**Description**: Clear, comprehensive description...
```

### Type-Specific Templates

See [references/body-templates.md](references/body-templates.md) for complete templates per issue type.

## Validation Checklist

Before creating any issue:

- [ ] Searched existing issues for duplicates
- [ ] Identified the correct prefix type
- [ ] Found the highest existing ID for that prefix
- [ ] Incremented to next sequential ID
- [ ] Formatted ID with 4-digit zero-padding
- [ ] Used appropriate type-specific template for body
- [ ] Title is under 72 characters and actionable

## Quick Reference Commands

```bash
# Find next ID for a prefix (replace FR with desired prefix)
PREFIX="FR" && gh issue list --search "$PREFIX-" --state all --limit 500 --json title --jq '.[].title' | grep -oE "$PREFIX-[0-9]+" | sort -t'-' -k2 -n | tail -1 | awk -F'-' -v p="$PREFIX" '{printf "%s-%04d\n", p, $2+1}'

# Search for existing issues by keyword
gh issue list --search "keyword" --state all --limit 50

# Check if issue with ID already exists
gh issue list --search "FR-0043" --state all
```
