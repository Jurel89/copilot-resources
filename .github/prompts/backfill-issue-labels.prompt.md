---
description: 'Backfill labels on all unlabeled GitHub issues by analyzing issue descriptions and related PR file changes using the canonical label taxonomy'
name: 'backfill-issue-labels'
agent: 'The GitChaos Organizer'
tools: ['execute', 'read', 'search', 'todo']
argument-hint: 'Optional: limit to specific issue numbers (e.g., "42 55 78") or a label namespace (e.g., "type"). Leave empty for all unlabeled issues.'
---

# Backfill Issue Labels from Descriptions and PR File Changes

## Mission

Identify every GitHub issue in the repository that has **no labels** (or is missing required labels), analyze each issue's **description** and the **files changed in linked PRs**, then apply the correct labels from the **canonical label taxonomy** defined in `.github/skills/github-labels/references/labels.yml`.

This is a **backfill operation** — it brings historical issues into compliance with the current labeling standard without modifying issue titles, descriptions, or any other metadata.

---

## Scope & Preconditions

- **Source of truth for labels**: `.github/skills/github-labels/references/labels.yml` — you MUST read this file before applying ANY label.
- **Target**: All open AND closed issues in the current repository that lack labels (or lack required labels).
- **Non-destructive**: This prompt ONLY adds labels. It does NOT remove existing labels, edit issue titles, or modify descriptions.
- **Required labels per issue**: At minimum, one `type:*` label and one `priority:*` label (as defined by the taxonomy rules).
- **Recommended labels**: `area:*` and `status:*` when determinable from context.
- **Scope filter**: If the user provides specific issue numbers, restrict operations to those issues only. If the user provides a namespace (e.g., `type`), only backfill labels in that namespace.
- **Idempotent**: Running this prompt multiple times produces the same end state — issues already correctly labeled are skipped.

---

## ⛔ PRE-FLIGHT PROTOCOL (MANDATORY)

> Complete these steps BEFORE any label operation. Do NOT skip.

### Step 0: Load the Label Taxonomy

1. Read the full contents of `.github/skills/github-labels/references/labels.yml`.
2. Parse every label entry from all `groups[].labels[]`.
3. Build a **label catalog** organized by namespace (`type:`, `priority:`, `area:`, `status:`, `domain:`, etc.).
4. Internalize the taxonomy rules:
   - **Required groups**: `type` (exclusive — pick ONE) and `priority` (exclusive — pick ONE).
   - **Recommended groups**: `area` (non-exclusive — multiple OK) and `status`.
   - **Max labels per issue**: 9.
5. Report: _"Label taxonomy loaded: N labels across M groups."_

**VALIDATION GATE**: If you have NOT read `labels.yml` in this session, STOP. You cannot proceed.

### Step 1: Internalize File Type Classification Rules

These rules override any heuristic based on file extension or content:

| File Pattern | Type Label | Area Label | NEVER Use |
|--------------|------------|------------|-----------|
| `.github/**/*.agent.md` | `type:chore` or `type:feature` | `area:ai` | ~~`type:docs`~~, ~~`area:docs`~~ |
| `.github/**/*.instructions.md` | `type:chore` or `type:feature` | `area:ai` | ~~`type:docs`~~, ~~`area:docs`~~ |
| `.github/**/SKILL.md` | `type:chore` or `type:feature` | `area:ai` | ~~`type:docs`~~, ~~`area:docs`~~ |
| `.github/**/*.prompt.md` | `type:chore` or `type:feature` | `area:ai` | ~~`type:docs`~~, ~~`area:docs`~~ |
| `.github/**/*.yml` (non-workflow config) | `type:chore` | `area:ai` | ~~`type:docs`~~, ~~`area:docs`~~ |
| `.github/workflows/**` | `type:ci` | `area:infra` | ~~`type:docs`~~, ~~`area:docs`~~ |
| `docs/**/*.md` | `type:docs` | `area:docs` | ✓ Correct |
| `README.md`, `CHANGELOG.md`, `CONTRIBUTING.md` | `type:docs` | `area:docs` | ✓ Correct |
| `*.ts`, `*.js`, `*.tsx`, `*.jsx` | Determined by description | `area:frontend` or `area:backend` | — |
| `*.py`, `*.go`, `*.java`, `*.rs` | Determined by description | `area:backend` | — |
| `*.sh` | Determined by description | `area:infra` or `area:devex` | — |

**CRITICAL**: The `area:*` label is based on FILE LOCATION, not content description. A markdown file about "documentation safety" in `.github/instructions/` gets `area:ai`, NOT `area:docs`.

---

## Workflow

Execute all phases **sequentially**. Use the todo list tool to track progress across phases.

### Phase 1: Discover Unlabeled Issues

#### 1.1 Fetch All Issues Without Labels

```bash
# Get all open issues with no labels
gh issue list --state open --label "" --limit 500 --json number,title,body,labels

# Get all closed issues with no labels
gh issue list --state closed --label "" --limit 500 --json number,title,body,labels
```

> **Note**: `gh issue list --label ""` returns issues with NO labels. If this syntax is not supported by the current `gh` version, fall back to fetching all issues and filtering client-side:

```bash
# Fallback: fetch all issues and filter for empty labels
gh issue list --state all --limit 500 --json number,title,body,labels | jq '[.[] | select(.labels | length == 0)]'
```

#### 1.2 Also Find Issues Missing Required Labels

Issues with SOME labels but missing required ones (`type:*` or `priority:*`) also need backfilling:

```bash
# Fetch ALL issues with their labels
gh issue list --state all --limit 500 --json number,title,body,labels
```

Filter for issues where:
- `labels` array is empty, OR
- No label starts with `type:`, OR
- No label starts with `priority:`

#### 1.3 Build the Work Queue

Create a list of issues to process, sorted by issue number (ascending). For each issue, record:
- Issue number
- Issue title
- Issue body (description)
- Current labels (if any)
- What's missing: labels entirely, `type:*`, `priority:*`, or both

Report: _"Found N issues requiring label backfill (X with no labels, Y missing type, Z missing priority)."_

If the user provided specific issue numbers, filter to only those issues.

---

### Phase 2: Analyze Each Issue

For **every issue** in the work queue, perform the following analysis:

#### 2.1 Analyze the Issue Description

Read the issue title and body to determine:

1. **Type classification** — Use the Label Decision Tree from the github-labels skill:
   - Is something broken? → `type:bug`
   - New capability? → `type:feature`
   - Improving existing? → `type:enhancement`
   - Internal restructuring? → `type:refactor`
   - CI/build/deployment? → `type:ci`
   - Performance/speed? → `type:performance`
   - Security? → `type:security`
   - Documentation only? → `type:docs`
   - Test code only? → `type:test`
   - Routine maintenance? → `type:chore`
   - Exploration/research? → `type:research`
   - Decision needed? → `type:proposal`

2. **Priority assessment** — Based on language, urgency signals, and impact:
   - Critical/urgent/outage/breaking → `priority:p0`
   - High impact/significant/important → `priority:p1`
   - Normal/standard/moderate → `priority:p2`
   - Low/nice-to-have/minor → `priority:p3`
   - When uncertain, default to `priority:p2`

3. **Keyword extraction** — Look for mentions of components, technologies, or domains that map to `area:*` or `domain:*` labels.

#### 2.2 Find and Analyze Linked PRs

For each issue, discover related PRs:

```bash
# Method 1: Search for PRs that mention the issue number
gh pr list --state all --search "#{ISSUE_NUMBER}" --limit 10 --json number,title,files

# Method 2: Check the issue timeline for linked PRs
gh api repos/{owner}/{repo}/issues/{ISSUE_NUMBER}/timeline --jq '.[] | select(.event == "cross-referenced" or .event == "connected") | .source.issue.number // .source.issue.pull_request.url'
```

If a timeline API call is complex, use this simpler approach:

```bash
# Search PRs by issue reference in title or body
gh pr list --state all --limit 500 --json number,title,body | jq --arg num "ISSUE_NUMBER" '[.[] | select(.title + " " + .body | test("#" + $num + "\\b"))]'
```

#### 2.3 Analyze Files Changed in Related PRs

For each linked PR, get the list of changed files:

```bash
gh pr view {PR_NUMBER} --json files --jq '.files[].path'
```

Use the changed files to determine:

1. **`area:*` labels** — Apply the File Type Classification Rules:
   - `.github/` config files → `area:ai`
   - `.github/workflows/` → `area:infra` (with `area:ci-cd`)
   - `docs/` → `area:docs`
   - Frontend code (`.tsx`, `.jsx`, `.css`, `.html`) → `area:frontend`
   - Backend code (`.py`, `.go`, `.java`, `.rs`, server-side `.ts`) → `area:backend`
   - Infrastructure files (Terraform, Docker, Kubernetes) → `area:infra`
   - CLI tools → `area:cli`
   - Test files only → supports `type:test` classification
   - Mixed areas → apply multiple `area:*` labels (non-exclusive)

2. **Refine type classification** — If the issue description was ambiguous, the file changes can clarify:
   - Only test files changed → `type:test`
   - Only `.md` docs changed → `type:docs` (unless in `.github/` config)
   - Only CI workflows changed → `type:ci`
   - Dependency files changed (package.json, go.mod) → may indicate `type:chore`

3. **`domain:*` labels** — If files relate to a specific product domain, apply the relevant domain label.

#### 2.4 Compile the Label Set

For each issue, produce a final label set that includes:

| Label Group | Required | How Determined |
|-------------|----------|----------------|
| `type:*` | YES (exactly one) | Issue description + file changes |
| `priority:*` | YES (exactly one) | Issue description urgency signals |
| `area:*` | Recommended | File changes in linked PRs |
| `status:*` | Optional | Issue state (closed → skip status) |
| `domain:*` | Optional | Content and file patterns |
| `effort:*` | Optional | Only if clearly determinable |
| `impact:*` | Optional | Only if clearly determinable |

**Validation before applying**:
- [ ] Every label exists in `labels.yml` (grep to confirm)
- [ ] Exactly ONE `type:*` label
- [ ] Exactly ONE `priority:*` label
- [ ] No more than 9 labels total
- [ ] Exclusive groups have at most one label each
- [ ] File classification rules were applied for `area:*` determination

---

### Phase 3: Apply Labels

#### 3.1 Present the Backfill Plan

Before applying any labels, present a summary table:

```
## Label Backfill Plan

| Issue # | Title | Labels to Add | Source |
|---------|-------|---------------|--------|
| #12 | Fix login error | type:bug, priority:p1, area:frontend | Description + PR #15 files |
| #18 | Add export feature | type:feature, priority:p2, area:backend | Description only |
| #25 | Update README | type:docs, priority:p3, area:docs | Description + PR #26 files |
| ... | ... | ... | ... |

**Total: N issues to label**
```

#### 3.2 Apply Labels to Each Issue

For each issue in the plan, apply the computed labels:

```bash
gh issue edit {ISSUE_NUMBER} --add-label "type:bug,priority:p1,area:frontend"
```

- Use `--add-label` (NOT `--label`) to ADD labels without removing existing ones.
- Apply all labels for an issue in a single command (comma-separated).
- Verify the command succeeded before moving to the next issue.
- If a label does not exist in the repository, log a warning and skip that label (run the `sync-labels` prompt first to create missing labels).
- If a command fails, log the error and continue with the next issue.

#### 3.3 Handle Conflicts

If an issue already has a label in an exclusive group (e.g., already has `type:feature` but analysis suggests `type:bug`):
- **Do NOT override** the existing label.
- Log the conflict: _"Issue #X already has type:feature — skipping type:bug suggestion."_
- Only add labels for groups that are currently absent.

---

### Phase 4: Verification

#### 4.1 Re-check for Unlabeled Issues

```bash
gh issue list --state all --limit 500 --json number,labels | jq '[.[] | select(.labels | length == 0)] | length'
```

Confirm the count of unlabeled issues has decreased to zero (or to only issues that were intentionally skipped).

#### 4.2 Validate Required Labels Coverage

```bash
# Check for issues still missing type: labels
gh issue list --state all --limit 500 --json number,labels | jq '[.[] | select(.labels | map(.name) | map(startswith("type:")) | any | not)] | length'

# Check for issues still missing priority: labels
gh issue list --state all --limit 500 --json number,labels | jq '[.[] | select(.labels | map(.name) | map(startswith("priority:")) | any | not)] | length'
```

Report any remaining gaps.

---

### Phase 5: Final Report

Present a structured summary:

```
## Label Backfill Complete ✅

**Repository**: <owner>/<repo>
**Date**: <current date>
**Taxonomy version**: <version from labels.yml>

### Summary

| Metric | Count |
|--------|-------|
| Issues analyzed | N |
| Issues labeled | X |
| Issues skipped (already labeled) | Y |
| Issues with conflicts (partial label) | Z |
| Labels applied (total) | T |

### Breakdown by Type

| Type Label | Issues |
|------------|--------|
| type:bug | A |
| type:feature | B |
| type:chore | C |
| type:docs | D |
| ... | ... |

### Breakdown by Area

| Area Label | Issues |
|------------|--------|
| area:ai | A |
| area:frontend | B |
| area:backend | C |
| area:docs | D |
| ... | ... |

### Issues Labeled

| Issue # | Title | Labels Added | Analysis Source |
|---------|-------|-------------|----------------|
| #12 | Fix login error | type:bug, priority:p1, area:frontend | Description + PR #15 |
| #18 | Add export feature | type:feature, priority:p2, area:backend | Description only |
| ... | ... | ... | ... |

### Conflicts / Warnings

| Issue # | Warning |
|---------|---------|
| #30 | Already has type:feature — skipped type:enhancement suggestion |
| #45 | Label "area:mobile" not found in taxonomy — skipped |

### Verification
✅ All processed issues now have required labels (type + priority)
✅ No labels were applied outside the canonical taxonomy
✅ No existing labels were removed or overridden
```

---

## Output Expectations

- **Format**: Structured Markdown report in chat.
- **Completeness**: Every unlabeled issue must be analyzed and either labeled or explicitly skipped with a reason.
- **Accuracy**: Labels must be chosen based on evidence (description text, PR file changes), never guessed arbitrarily.
- **Safety**: Only ADD labels. Never remove existing labels. Never modify issue content.
- **Taxonomy compliance**: Every applied label must exist in `labels.yml`. Zero invented labels.

## Quality Assurance

- [ ] Label taxonomy file was read and fully parsed BEFORE any analysis.
- [ ] File Type Classification Rules were applied for all `area:*` determinations.
- [ ] ALL issues without labels were discovered (both open and closed).
- [ ] Issues missing required labels (type/priority) were also included.
- [ ] Each issue's description was analyzed for type and priority signals.
- [ ] Linked PRs were discovered and their file changes analyzed for area labels.
- [ ] Every label applied exists in the canonical taxonomy (verified by grep).
- [ ] Exclusive groups have at most one label per issue.
- [ ] No more than 9 labels were applied to any single issue.
- [ ] Existing labels were preserved — only `--add-label` was used, never `--label`.
- [ ] Conflicts with existing exclusive labels were logged, not overridden.
- [ ] Final verification confirmed reduction in unlabeled issues.
- [ ] The `sync-labels` prompt should be run BEFORE this prompt to ensure all taxonomy labels exist in the repository.
