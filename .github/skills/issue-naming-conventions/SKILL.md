---
name: issue-naming-conventions
description: >
  Canonical GitHub issue naming and requirement ID system. Use when creating issues,
  assigning traceable IDs (FR-####, BG-####, HF-####, TC-####, SC-####, PF-####, DC-####,
  IN-####), or ensuring issue traceability. CRITICAL: Only prefixes defined in
  issue-types.yml are allowed. Triggers on: create issue, new issue, assign ID,
  requirement ID, issue prefix, check existing issues, increment issue number, ID
  conventions, issue title, issue naming.
license: Complete terms in LICENSE.txt
---

# Issue Naming Conventions Skill

Apply consistent, traceable requirement IDs to GitHub issues using a **canonical naming taxonomy**. This skill ensures issues are standardized across repositories and enables full traceability from requirements to implementation.

## ⛔ MANDATORY PRE-FLIGHT PROTOCOL

> **STOP! Before ANY issue creation or ID assignment, you MUST complete this checklist:**

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│  ISSUE ID ASSIGNMENT PRE-FLIGHT CHECKLIST (MANDATORY)                      │
├─────────────────────────────────────────────────────────────────────────────┤
│  □ Step 1: READ the taxonomy file                                          │
│            read_file .github/skills/issue-naming-conventions/references/   │
│                      issue-types.yml                                       │
│                                                                             │
│  □ Step 2: IDENTIFY the correct prefix for the issue type                  │
│            Match user intent to FR/BG/HF/TC/SC/PF/DC/IN                    │
│                                                                             │
│  □ Step 3: QUERY existing issues to find the highest ID                    │
│            gh issue list --search "PREFIX-" --state all --limit 500        │
│                                                                             │
│  □ Step 4: INCREMENT to next sequential number, zero-pad to 4 digits       │
│            If highest is FR-0042, next is FR-0043                          │
│                                                                             │
│  □ Step 5: VALIDATE the ID doesn't already exist before creating           │
│            gh issue list --search "PREFIX-NNNN" --state all                │
│                                                                             │
│  ⚠️  SKIPPING ANY STEP = PROTOCOL VIOLATION                                │
└─────────────────────────────────────────────────────────────────────────────┘
```

**If you haven't queried existing issues for the highest ID, you CANNOT create an issue.**

## Critical Constraints

> **⚠️ ABSOLUTE RULE #1: You MUST NEVER create an issue without first querying existing issues to determine the next sequential ID.**

> **⚠️ ABSOLUTE RULE #2: You MUST NEVER reuse an ID, even for closed or deleted issues. IDs are permanently allocated.**

> **⚠️ ABSOLUTE RULE #3: You MUST NEVER invent new prefixes. Only use prefixes defined in [issue-types.yml](./references/issue-types.yml).**

If a user requests an issue type that doesn't fit existing prefixes:

1. Explain that no matching prefix exists
2. Suggest the closest matching prefix from the taxonomy
3. If truly no match, use TC (Technical Chore) as a fallback and note it in the issue

## When to Use This Skill

- User asks to **create a new issue** with proper ID
- User asks to **assign a requirement ID** to work items
- User asks to **determine the next ID** for a specific type
- User asks about **issue naming conventions** or standards
- User needs to **ensure traceability** across the codebase
- User mentions **FR, BG, HF, TC, SC, PF, DC, or IN** prefixes
- User asks to **triage or categorize** incoming work
- User asks how to **format an issue title**

## Issue Type Overview

All issues follow a **[PREFIX-####] Title** pattern. The taxonomy is defined in [issue-types.yml](./references/issue-types.yml).

### Requirement Type Prefixes

| Prefix | Type | Use For | Example |
| ------ | ---- | ------- | ------- |
| `FR-####` | Feature Request | Net-new user-facing capabilities | `[FR-0043] Add dark mode theme support` |
| `BG-####` | Bug | Non-critical defects, broken functionality | `[BG-0089] Login fails when SSO is enabled` |
| `HF-####` | Hotfix | Critical production issues, P0 emergencies | `[HF-0015] Critical: API returns 500 on all requests` |
| `TC-####` | Technical Chore | Refactoring, tech debt, maintenance, **config files** | `[TC-0023] Refactor authentication module` |
| `SC-####` | Security | Vulnerabilities, compliance, security fixes | `[SC-0007] Update dependencies with known CVEs` |
| `PF-####` | Performance | Optimization, latency, throughput | `[PF-0011] Optimize database queries for dashboard` |
| `DC-####` | Documentation | **Only** `docs/`, `README.md` — NOT agents/skills/instructions | `[DC-0034] Add API reference for v2 endpoints` |
| `IN-####` | Infrastructure | CI/CD, deployment, DevOps | `[IN-0019] Configure staging environment CI/CD` |

**CRITICAL**: Changes to `.github/` configuration files (agents, skills, instructions, prompts) should use `TC-####` (Technical Chore) or `FR-####` (Feature Request), **NEVER** `DC-####` (Documentation). These files are configuration/tooling, not documentation.

### ID Format Rules

- **Always 4-digit zero-padded**: `FR-0001`, not `FR-1`
- **Range**: 0001-9999 per prefix type
- **Permanent allocation**: IDs are never reused, even for closed issues
- **Title format**: `[PREFIX-####] Descriptive Title`
- **Max title length**: 72 characters (including prefix)

## Step-by-Step Workflows

### Workflow 1: Create a New Issue with Proper ID

1. **Read the taxonomy**: Load [issue-types.yml](./references/issue-types.yml) to identify the correct prefix
2. **Match user intent**: Determine which prefix fits (see type descriptions)
3. **Query existing issues**: Find the highest ID for that prefix

   ```bash
   gh issue list --search "FR-" --state all --limit 500 --json title --jq '.[].title' | grep -oE 'FR-[0-9]+' | sort -t'-' -k2 -n | tail -1
   ```

4. **Increment the ID**: If highest is `FR-0042`, next is `FR-0043`
5. **Validate uniqueness**: Confirm the ID doesn't exist

   ```bash
   gh issue list --search "FR-0043" --state all
   ```

6. **Select template**: Use the appropriate template from [body-templates.md](./references/body-templates.md)
7. **Create the issue**: Apply proper title format and body template

**Example Output:**

```text
Next available Feature Request ID: FR-0043

Issue title: [FR-0043] Add dark mode theme support

Using template: Feature Request (Full)
- Includes: Summary, User Story, Problem Statement, Proposed Solution, Acceptance Criteria
```

### Workflow 2: Find Next Available ID

1. **Identify the prefix type** from user request
2. **Execute the query**:

   ```bash
   PREFIX="FR" && gh issue list --search "$PREFIX-" --state all --limit 500 --json title --jq '.[].title' | grep -oE "$PREFIX-[0-9]+" | sort -t'-' -k2 -n | tail -1 | awk -F'-' -v p="$PREFIX" '{printf "%s-%04d\n", p, $2+1}'
   ```

3. **If no issues exist** with that prefix, start at `0001`
4. **Report the result** with confidence

**Example:**

```text
Query: Find next Bug ID
Result: BG-0090 (current highest: BG-0089)
```

### Workflow 3: Validate an Existing ID

1. **Parse the ID** from user input
2. **Check format validity**: Must match `^[A-Z]{2}-[0-9]{4}$`
3. **Verify prefix exists** in taxonomy
4. **Check issue existence**:

   ```bash
   gh issue list --search "FR-0043" --state all --json number,title,state
   ```

5. **Report findings**:
   - If exists: Return issue details
   - If not: Confirm ID is available for use

### Workflow 4: Suggest Correct Prefix for Work Item

1. **Analyze the work description** provided by user
2. **Match against type definitions** in [issue-types.yml](./references/issue-types.yml)
3. **Consider keywords**: Each type has associated keywords
4. **Apply exclusion rules**: Check "when_not_to_use" guidance
5. **Recommend the prefix** with justification

**Example:**

```text
Work: "The dashboard is loading slowly, taking 5+ seconds"
Analysis: Performance issue with measurable metric
Recommendation: PF (Performance)
Justification: Latency/speed issue, not a bug (functional), has measurable target
```

## Quick Reference Commands

### Find Next ID for Any Prefix

```bash
# Replace PREFIX with: FR, BG, HF, TC, SC, PF, DC, or IN
PREFIX="FR" && gh issue list --search "$PREFIX-" --state all --limit 500 --json title --jq '.[].title' | grep -oE "$PREFIX-[0-9]+" | sort -t'-' -k2 -n | tail -1 | awk -F'-' -v p="$PREFIX" '{printf "%s-%04d\n", p, $2+1}'
```

### Check if ID Exists

```bash
gh issue list --search "FR-0043" --state all --json number,title,state
```

### List All Issues with Prefix

```bash
gh issue list --search "BG-" --state all --limit 200 --json number,title,state
```

### Get Highest ID for Each Prefix

```bash
for PREFIX in FR BG HF TC SC PF DC IN; do
  HIGHEST=$(gh issue list --search "$PREFIX-" --state all --limit 500 --json title --jq '.[].title' | grep -oE "$PREFIX-[0-9]+" | sort -t'-' -k2 -n | tail -1)
  echo "$PREFIX: ${HIGHEST:-$PREFIX-0000 (none)}"
done
```

## Troubleshooting

| Issue | Solution |
| ----- | -------- |
| No issues found for prefix | Start numbering at 0001 |
| User requests unknown prefix | Map to closest existing prefix, suggest TC as fallback |
| ID already exists | Query was stale; re-run and increment |
| Title exceeds 72 characters | Shorten the descriptive portion, keep prefix |
| Unsure which type to use | Consult type keywords and when_to_use in YAML |
| Need to change issue type | Create new issue with correct prefix, close old one |

## References

- [Issue Type Definitions (YAML)](./references/issue-types.yml) — Canonical taxonomy
- [Issue Body Templates](./references/body-templates.md) — Full and minimal templates for each type
