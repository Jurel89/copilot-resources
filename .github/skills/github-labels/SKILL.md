---
name: github-labels
description: >
  Canonical GitHub issue and PR labeling using a strict taxonomy. Use when asked to
  label issues, suggest labels, create GitHub labels, triage issues, categorize work,
  or manage issue workflows. CRITICAL: Only labels defined in labels.yml are allowed;
  creating new labels is strictly prohibited. Supports type, area, status, priority,
  impact, effort, risk, domain, compliance, release, and contrib label namespaces.
license: Complete terms in LICENSE.txt
---

# GitHub Labels Skill

Apply consistent, well-defined labels to GitHub issues and pull requests using a **canonical label taxonomy**. This skill ensures labels are standardized across repositories and prevents label sprawl.

## Critical Constraint

> **⚠️ ABSOLUTE RULE: You MUST NEVER create, suggest, or apply any label that is not defined in [labels.yml](./references/labels.yml).**

If a user requests a label that doesn't exist:

1. Explain that the label is not in the approved taxonomy
2. Suggest the closest matching label(s) from the taxonomy
3. If no match exists, recommend opening a "Label Change Proposal" issue

## When to Use This Skill

- User asks to **label an issue or PR**
- User asks to **suggest labels** for work items
- User asks to **triage issues** or classify incoming work
- User asks to **create or set up labels** in a repository
- User asks about **label meanings** or which label to use
- User wants to **categorize or organize issues**
- User mentions **priority, type, status, effort, or risk** of issues
- User asks to **sync labels** across repositories

## Label Taxonomy Overview

All labels follow a **namespace:value** pattern. The taxonomy is defined in [labels.yml](./references/labels.yml).

### Required Labels (Every Issue MUST Have)

| Namespace | Purpose | Exclusive | Example |
|-----------|---------|-----------|---------|
| `type:` | What kind of work (bug, feature, etc.) | Yes (pick ONE) | `type:bug` |
| `priority:` | Urgency level (p0-p3) | Yes (pick ONE) | `priority:p2` |

### Recommended Labels (Apply When Relevant)

| Namespace | Purpose | Exclusive | Example |
|-----------|---------|-----------|---------|
| `area:` | System component affected | No (multiple OK) | `area:frontend`, `area:backend` |
| `status:` | Workflow state/blockers | No (some coexist) | `status:ready` |

### Optional Labels (Enhance Context)

| Namespace | Purpose | Exclusive | Example |
|-----------|---------|-----------|---------|
| `impact:` | Who/what is affected | Yes | `impact:high` |
| `effort:` | Size estimation (xs-xl) | Yes | `effort:m` |
| `risk:` | Change risk level | Yes | `risk:low` |
| `domain:` | Product/initiative context | No | `domain:copilot` |
| `compliance:` | Governance requirements | No | `compliance:privacy` |
| `release:` | Semver changelog category | Yes | `release:minor` |
| `contrib:` | Community contribution markers | No | `contrib:good-first-issue` |

## Step-by-Step Workflows

### Workflow 1: Label a New Issue

1. **Read the taxonomy**: Load [labels.yml](./references/labels.yml) to understand available labels
2. **Identify type**: Determine the single best `type:*` label (REQUIRED)
3. **Assign priority**: Select one `priority:*` level (REQUIRED)
4. **Add area(s)**: Tag relevant `area:*` components if applicable
5. **Set status**: Apply `status:triage` for new issues or `status:ready` if well-defined
6. **Optional metadata**: Add effort, impact, risk, or domain labels if helpful
7. **Validate**: Ensure no more than 9 labels total; check exclusive constraints

**Example Output:**
```
Suggested labels for this issue:
- type:bug (REQUIRED) — This is a broken behavior
- priority:p1 (REQUIRED) — Significant user impact
- area:frontend — Affects the UI components
- area:backend — Also involves API changes
- status:ready — Issue is well-defined and actionable
- effort:m — Estimated 1-2 days of work
```

### Workflow 2: Create Labels in a New Repository

1. **Load taxonomy**: Read all labels from [labels.yml](./references/labels.yml)
2. **Generate commands**: Create `gh label create` commands for each label
3. **Include colors**: Use the `color` field for visual consistency
4. **Execute in order**: Create labels by namespace for organization

**Script generation example:**
```bash
# Type labels
gh label create "type:bug" --color "d73a4a" --description "Something is BROKEN, INCORRECT, or behaving UNEXPECTEDLY..."
gh label create "type:feature" --color "0075ca" --description "A NET-NEW user-facing CAPABILITY..."
# Continue for all labels...
```

### Workflow 3: Suggest Labels for a Description

1. **Analyze the issue/PR description** for keywords
2. **Map keywords to labels**:
   - "broken", "error", "crash" → `type:bug`
   - "add", "new feature", "implement" → `type:feature`
   - "improve", "better", "enhance" → `type:enhancement`
   - "urgent", "critical", "outage" → `priority:p0` or `priority:p1`
   - "slow", "performance", "optimize" → `type:performance`
3. **Consider context**: Repository type, mentioned components, severity
4. **Apply validation rules**: Check exclusivity, required groups

### Workflow 4: Triage Incoming Issues

1. **Apply `status:triage`** to new unclassified issues
2. **Identify type and priority** as the minimum required labels
3. **Request more info** if unclear → apply `status:needs-info`
4. **Mark as ready** when fully specified → replace `status:triage` with `status:ready`
5. **Remove `status:triage`** once classified

## Label Decision Tree

```
Is something broken/not working?
├─ Yes → type:bug
└─ No
   ├─ Is it a completely new capability?
   │  ├─ Yes → type:feature
   │  └─ No → Is it improving existing functionality?
   │     ├─ Yes → type:enhancement
   │     └─ No → Is it internal restructuring?
   │        ├─ Yes → type:refactor
   │        └─ No → Is it CI/build/deployment related?
   │           ├─ Yes → type:ci
   │           └─ No → Is it about performance/speed?
   │              ├─ Yes → type:performance
   │              └─ No → Is it security-related?
   │                 ├─ Yes → type:security
   │                 └─ No → Is it documentation only?
   │                    ├─ Yes → type:docs
   │                    └─ No → Is it test code only?
   │                       ├─ Yes → type:test
   │                       └─ No → Is it routine maintenance?
   │                          ├─ Yes → type:chore
   │                          └─ No → Is it exploration/research?
   │                             ├─ Yes → type:research
   │                             └─ No → type:proposal (if decision needed)
```

## Validation Rules

Before applying labels, verify:

- [ ] **Required labels present**: Exactly one `type:*` AND one `priority:*`
- [ ] **Exclusive groups respected**: Only one label from exclusive groups
- [ ] **Label exists in taxonomy**: Every label must be in [labels.yml](./references/labels.yml)
- [ ] **Max labels not exceeded**: No more than 9 labels per issue
- [ ] **Colors match**: Use hex colors from taxonomy for consistency

## Common Mistakes to Avoid

| Mistake | Why It's Wrong | Correct Approach |
|---------|----------------|------------------|
| Creating custom labels | Causes label sprawl and inconsistency | Use only labels from taxonomy |
| Missing type or priority | Violates required label rules | Always apply both |
| Multiple exclusive labels | e.g., `type:bug` AND `type:feature` | Pick the primary type |
| Using `type:bug` for slowness | Performance issues aren't bugs | Use `type:performance` |
| Applying `type:docs` to code+docs | Type should reflect primary change | Use primary type, mention docs |

## Troubleshooting

| Issue | Solution |
|-------|----------|
| User requests non-existent label | Explain constraint, suggest closest match |
| Unsure between two types | Ask clarifying questions or use primary intent |
| Label doesn't fit any category | Recommend opening "Label Change Proposal" |
| Need to update label colors | Edit [labels.yml](./references/labels.yml), re-sync repos |

## References

- [**Label Taxonomy (YAML)**](./references/labels.yml) — The canonical label definitions
- [GitHub CLI Label Commands](https://cli.github.com/manual/gh_label) — For creating/managing labels
