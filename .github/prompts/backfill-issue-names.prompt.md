---
description: 'Backfill issue titles on all GitHub issues to comply with the canonical [PREFIX-####] naming convention by analyzing, categorizing, and renaming non-compliant issues'
name: 'backfill-issue-names'
agent: 'The GitChaos Organizer'
tools: ['execute', 'read', 'search', 'todo']
argument-hint: 'Optional: limit to specific issue numbers (e.g., "42 55 78") or a prefix (e.g., "FR"). Leave empty for all issues.'
---

# Backfill Issue Names to Naming Convention Compliance

## Mission

Audit **every** GitHub issue in the repository, verify whether its title complies with the **canonical naming convention** `[PREFIX-####] Descriptive Title`, and **rename all non-compliant issues** with the correct prefix and a sequentially assigned, zero-padded ID — ensuring chronological ordering within each type and zero ID collisions.

This is a **backfill operation** — it brings historical issue titles into compliance with the naming standard defined in `.github/skills/issue-naming-conventions/`. Issue descriptions and labels are **NOT modified**.

---

## Scope & Preconditions

- **Source of truth for naming**: `.github/skills/issue-naming-conventions/SKILL.md` and `.github/skills/issue-naming-conventions/references/issue-types.yml` — you MUST read both files before any operation.
- **Target**: All open AND closed issues in the current repository whose titles do NOT match the pattern `[PREFIX-####] Descriptive Title`.
- **Already-compliant issues**: Issues whose titles already match `^\[[A-Z]{2}-[0-9]{4}\] .+$` are considered compliant. Their IDs are **reserved** and must never be reused.
- **Non-destructive to content**: This prompt ONLY renames issue titles. It does NOT edit issue bodies, change labels, close issues, or modify any other metadata.
- **Scope filter**: If the user provides specific issue numbers, restrict operations to those issues only. If the user provides a prefix (e.g., `FR`), only backfill issues that would be categorized under that prefix.
- **Idempotent**: Running this prompt multiple times produces the same end state — already-compliant issues are never touched.

---

## ⛔ PRE-FLIGHT PROTOCOL (MANDATORY)

> Complete ALL steps BEFORE any analysis or renaming. Do NOT skip any step.

### Step 0: Load the Issue Naming Conventions Skill

1. Read the full contents of `.github/skills/issue-naming-conventions/SKILL.md`.
2. Read the full contents of `.github/skills/issue-naming-conventions/references/issue-types.yml`.
3. Internalize:
   - The 8 valid prefixes: `FR`, `BG`, `HF`, `TC`, `SC`, `PF`, `DC`, `IN`.
   - The title pattern: `[PREFIX-####] Descriptive Title` (max 72 characters).
   - ID format: 4-digit zero-padded, range 0001–9999, **never reused**.
   - The `use_when`, `not_for`, and `keywords` for each prefix — these drive categorization.
   - The `.github/` file classification rule: config files (agents, skills, instructions, prompts) → `TC` or `FR`, **never** `DC`.
4. Report: _"Naming conventions loaded: 8 prefixes (FR, BG, HF, TC, SC, PF, DC, IN). Title pattern: [PREFIX-####] Descriptive Title. Max 72 chars."_

**VALIDATION GATE**: If you have NOT read both files, STOP. You cannot proceed.

---

## Workflow

Execute all phases **sequentially**. Use the todo list tool to track progress across phases.

---

### Phase 1: Discover All Issues and Identify Compliance Status

#### 1.1 Fetch Every Issue in the Repository

Retrieve ALL issues (open and closed) with their titles, numbers, creation dates, and current labels:

```bash
# Fetch all issues (open + closed), sorted by creation date ascending (oldest first)
gh issue list --state all --limit 1000 --json number,title,createdAt,labels,state --jq 'sort_by(.createdAt)'
```

> **Pagination**: If the repo has more than 1000 issues, paginate until all issues are retrieved. Never process a partial list.

#### 1.2 Classify Each Issue's Compliance Status

For every issue, check if its title matches the compliant pattern `^\[[A-Z]{2}-[0-9]{4}\] .+$`:

- **Compliant** — Title matches the pattern with a valid prefix (one of the 8 defined prefixes). Record the prefix and ID as **reserved**.
- **Partially compliant** — Title contains a prefix-like pattern but is malformed (e.g., `FR-43` instead of `FR-0043`, missing brackets, wrong format). These MUST be fixed.
- **Non-compliant** — Title has no recognizable prefix pattern. These MUST be categorized and renamed.

#### 1.3 Build the Reserved ID Registry

From all compliant issues, build a registry of **already-used IDs** per prefix:

```text
Reserved IDs:
  FR: [0001, 0003, 0007, 0012, ...]
  BG: [0001, 0002, 0005, ...]
  TC: [0001, ...]
  DC: [0001, 0002, ...]
  ... (all 8 prefixes)
```

Also determine the **current highest ID** for each prefix:

```bash
for PREFIX in FR BG HF TC SC PF DC IN; do
  HIGHEST=$(gh issue list --search "$PREFIX-" --state all --limit 500 --json title --jq '.[].title' | grep -oE "$PREFIX-[0-9]+" | sort -t'-' -k2 -n | tail -1)
  echo "$PREFIX: ${HIGHEST:-none}"
done
```

#### 1.4 Build the Work Queue

Create a list of non-compliant and partially-compliant issues, **sorted by creation date ascending** (oldest first). This chronological ordering is critical because IDs will be assigned sequentially from oldest to newest within each type.

For each issue, record:
- Issue number (`#N`)
- Current title
- Creation date
- Current labels (used as categorization hints)
- State (open/closed)

Report: _"Found N total issues: X compliant (reserved IDs), Y non-compliant, Z partially compliant. Work queue: Y + Z = W issues to process."_

If the user provided specific issue numbers or a prefix filter, apply the filter now.

---

### Phase 2: Categorize Every Non-Compliant Issue

For **every issue** in the work queue, determine the correct prefix. This is the most critical phase — accuracy here determines the quality of the entire backfill.

#### 2.1 Categorization Decision Tree

Analyze each issue's **title**, **body**, and **labels** to assign the correct prefix. Apply this decision tree IN ORDER — first match wins:

```text
1. Is it a critical production emergency / P0 / outage?
   → HF (Hotfix)

2. Is it a security vulnerability, CVE, compliance, or audit finding?
   → SC (Security)

3. Is something broken, crashing, or producing incorrect results?
   → BG (Bug)

4. Is it about CI/CD, deployment, pipelines, monitoring, or DevOps?
   → IN (Infrastructure)

5. Is it about speed, latency, throughput, or resource optimization?
   → PF (Performance)

6. Is it documentation-only (docs/, README, CHANGELOG, CONTRIBUTING)?
   ⚠️ CRITICAL CHECK: Does it reference .github/ config files (agents, skills, instructions, prompts)?
     → If YES: TC (Technical Chore) or FR (Feature Request) — NEVER DC
     → If NO (genuine docs): DC (Documentation)

7. Is it a net-new user-facing capability or integration?
   → FR (Feature Request)

8. Is it refactoring, tech debt, dependency updates, maintenance, or cleanup?
   → TC (Technical Chore)

9. None of the above clearly apply?
   → TC (Technical Chore) — the fallback prefix
```

#### 2.2 Use All Available Signals

For each issue, examine:

1. **Title keywords** — Match against the `keywords` field in `issue-types.yml`
2. **Issue body** — Read the full body for context clues:
   ```bash
   gh issue view {ISSUE_NUMBER} --json body --jq '.body'
   ```
3. **Labels** — Existing labels provide strong hints:
   - `type:bug` → `BG`
   - `type:feature` → `FR`
   - `type:chore` → `TC`
   - `type:docs` → `DC` (unless `.github/` config)
   - `type:ci` → `IN`
   - `type:performance` → `PF`
   - `type:security` → `SC`
   - `priority:p0` → consider `HF`
4. **Linked PRs and changed files** — If determinable, the files changed can clarify categorization:
   ```bash
   gh pr list --state all --search "#{ISSUE_NUMBER}" --limit 5 --json number,title,files
   ```

#### 2.3 Record the Categorization Decision

For each issue, record:
- **Assigned prefix** (e.g., `FR`)
- **Confidence level**: `high` (strong signals), `medium` (reasonable inference), `low` (fallback)
- **Reasoning**: Brief justification (e.g., "Title mentions 'add dark mode', body describes new capability → FR")

---

### Phase 3: Assign Sequential IDs (Chronological Order)

This phase assigns the actual `PREFIX-####` IDs. The ordering rule is absolute:

> **Within each prefix type, IDs MUST be assigned in chronological order of issue creation date — oldest issue gets the lowest next available ID.**

#### 3.1 Group Issues by Prefix

From the categorization results, group all non-compliant issues by their assigned prefix:

```text
FR group (sorted by createdAt ascending):
  1. #12 (created 2025-06-01) → needs FR ID
  2. #45 (created 2025-08-15) → needs FR ID
  3. #78 (created 2025-11-03) → needs FR ID

BG group (sorted by createdAt ascending):
  1. #8  (created 2025-05-20) → needs BG ID
  2. #33 (created 2025-07-10) → needs BG ID

TC group (sorted by createdAt ascending):
  1. #5  (created 2025-04-12) → needs TC ID
  ...
```

**CRITICAL**: Each group MUST be sorted by `createdAt` ascending. The oldest issue in each group gets the next available ID after the current highest.

#### 3.2 Assign IDs Sequentially

For each prefix group, starting from the **current highest ID + 1**:

```text
Example — FR group:
  Current highest reserved FR ID: FR-0012
  
  #12 (oldest)  → FR-0013
  #45 (middle)  → FR-0014
  #78 (newest)  → FR-0015

Example — BG group:
  Current highest reserved BG ID: BG-0005
  
  #8  (oldest)  → BG-0006
  #33 (newer)   → BG-0007
```

If **no issues** currently exist for a prefix, start at `0001`.

#### 3.3 Validate ID Uniqueness

After assigning all IDs, perform a collision check:

- No ID appears twice across all assignments.
- No assigned ID conflicts with any reserved ID from Phase 1.
- Every ID follows the 4-digit zero-padded format.

```bash
# Spot-check: verify assigned IDs don't already exist
gh issue list --search "FR-0013" --state all --json number,title
```

#### 3.4 Compose New Titles

For each issue, construct the new compliant title:

```text
Format: [PREFIX-####] Descriptive Title

Rules:
- Preserve the original descriptive intent of the title
- Clean up redundant words, fix grammar, capitalize properly
- Remove any existing malformed prefix attempts
- Ensure total title length ≤ 72 characters (including [PREFIX-####] )
- The descriptive portion should be clear and concise
```

**Examples:**

| Issue # | Original Title | New Title |
|---------|---------------|-----------|
| #12 | Add dark mode to the app | [FR-0013] Add dark mode theme support |
| #8 | login is broken when using SSO | [BG-0006] Login fails when SSO is enabled |
| #5 | clean up old code | [TC-0001] Remove deprecated authentication code |

---

### Phase 4: Present the Backfill Plan (Mandatory Review)

Before renaming ANY issue, present the **complete plan** for review. This is a mandatory checkpoint.

#### 4.1 Summary Table

```markdown
## Issue Name Backfill Plan

**Repository**: <owner>/<repo>
**Date**: <current date>
**Naming convention**: [PREFIX-####] Descriptive Title (max 72 chars)

### Compliance Status

| Metric | Count |
|--------|-------|
| Total issues in repository | N |
| Already compliant (skipped) | X |
| Non-compliant (to be renamed) | Y |
| Partially compliant (to be fixed) | Z |

### Reserved ID Registry

| Prefix | Highest Existing ID | IDs to Assign | New Highest |
|--------|-------------------|---------------|-------------|
| FR | FR-0012 | 3 (0013–0015) | FR-0015 |
| BG | BG-0005 | 2 (0006–0007) | BG-0007 |
| TC | none | 4 (0001–0004) | TC-0004 |
| ... | ... | ... | ... |

### Rename Plan (sorted by prefix, then by creation date)

| Issue # | Created | Current Title | → New Title | Prefix | Confidence |
|---------|---------|---------------|-------------|--------|------------|
| #5 | 2025-04-12 | clean up old code | [TC-0001] Remove deprecated authentication code | TC | high |
| #12 | 2025-06-01 | Add dark mode | [FR-0013] Add dark mode theme support | FR | high |
| #8 | 2025-05-20 | login broken with SSO | [BG-0006] Login fails when SSO is enabled | BG | high |
| #33 | 2025-07-10 | another bug somewhere | [BG-0007] Intermittent crash on dashboard load | BG | medium |
| ... | ... | ... | ... | ... | ... |
```

#### 4.2 Flag Low-Confidence Categorizations

Highlight any issue where confidence is `low` or `medium`, explaining why and what alternative prefix was considered:

```markdown
### ⚠️ Uncertain Categorizations

| Issue # | Assigned | Alternative | Reason |
|---------|----------|-------------|--------|
| #33 | BG (Bug) | PF (Performance) | Title is ambiguous — "app is slow" could be bug or perf |
| #67 | TC (Chore) | FR (Feature) | Cleanup that also adds minor capability |
```

**CRITICAL**: After presenting the plan, **PAUSE and ask the user to confirm** before proceeding to Phase 5. Say:

> _"Please review the backfill plan above. Confirm to proceed with renaming, or indicate any issues you'd like me to re-categorize before applying changes."_

---

### Phase 5: Apply Renames

After user confirmation, rename each issue by updating its title.

#### 5.1 Rename Each Issue

For each issue in the approved plan:

```bash
gh issue edit {ISSUE_NUMBER} --title "[PREFIX-####] Descriptive Title"
```

- Process issues **one at a time** in the order presented in the plan.
- Verify each rename succeeded before moving to the next:
  ```bash
  gh issue view {ISSUE_NUMBER} --json title --jq '.title'
  ```
- If a rename fails, log the error and continue with the next issue. Do not halt the entire operation.
- Track progress using the todo list tool.

#### 5.2 Handle Edge Cases

| Situation | Action |
|-----------|--------|
| Issue is locked | Log warning, skip, report in final summary |
| Title exceeds 72 chars after adding prefix | Shorten the descriptive portion while preserving meaning |
| Issue has been renamed by someone else mid-operation | Re-check compliance, skip if now compliant |
| API rate limiting | Wait and retry with exponential backoff |
| Partially compliant (malformed prefix) | Replace with correct format (e.g., `FR-43` → `[FR-0043]`) |

---

### Phase 6: Verification

#### 6.1 Re-check All Issues

After all renames, verify compliance across the entire repository:

```bash
# Fetch all issue titles and check compliance
gh issue list --state all --limit 1000 --json number,title --jq '.[].title' | grep -cvE '^\[[A-Z]{2}-[0-9]{4}\]'
```

The count of non-compliant titles should match the number of issues that were intentionally skipped (locked, errored, etc.).

#### 6.2 Validate ID Sequence Integrity

Confirm no duplicate IDs were created:

```bash
for PREFIX in FR BG HF TC SC PF DC IN; do
  DUPES=$(gh issue list --search "$PREFIX-" --state all --limit 500 --json title --jq '.[].title' | grep -oE "$PREFIX-[0-9]+" | sort | uniq -d)
  if [ -n "$DUPES" ]; then
    echo "DUPLICATE IDs found for $PREFIX: $DUPES"
  else
    echo "$PREFIX: No duplicates ✅"
  fi
done
```

#### 6.3 Validate No Gaps in Newly Assigned IDs

Within the newly assigned ID range for each prefix, verify there are no unexpected gaps:

```bash
# Example for FR — check that 0013, 0014, 0015 all exist
for ID in 0013 0014 0015; do
  gh issue list --search "FR-$ID" --state all --json number,title
done
```

---

### Phase 7: Final Report

Present a structured summary of all operations performed:

```markdown
## Issue Name Backfill Complete ✅

**Repository**: <owner>/<repo>
**Date**: <current date>
**Convention**: [PREFIX-####] Descriptive Title (max 72 chars)

### Summary

| Metric | Count |
|--------|-------|
| Total issues audited | N |
| Already compliant (skipped) | X |
| Successfully renamed | Y |
| Failed to rename | F |
| Skipped (locked/errored) | S |

### New IDs Assigned by Prefix

| Prefix | Type | IDs Assigned | Range |
|--------|------|-------------|-------|
| FR | Feature Request | 3 | 0013–0015 |
| BG | Bug | 2 | 0006–0007 |
| TC | Technical Chore | 4 | 0001–0004 |
| ... | ... | ... | ... |

### All Renames Performed

| Issue # | Before | After | Status |
|---------|--------|-------|--------|
| #5 | clean up old code | [TC-0001] Remove deprecated authentication code | ✅ Renamed |
| #12 | Add dark mode | [FR-0013] Add dark mode theme support | ✅ Renamed |
| #8 | login broken with SSO | [BG-0006] Login fails when SSO is enabled | ✅ Renamed |
| ... | ... | ... | ... |

### Errors / Warnings

| Issue # | Problem | Resolution |
|---------|---------|------------|
| #99 | Issue is locked | Skipped — requires manual rename |
| ... | ... | ... |

### Verification
✅ All processed issues now have compliant titles
✅ No duplicate IDs exist across any prefix
✅ ID sequences are contiguous within newly assigned ranges
✅ No reserved IDs were reused
✅ All titles are ≤ 72 characters
```

---

## Output Expectations

- **Format**: Structured Markdown report in chat.
- **Completeness**: Every issue in the repository must be audited and either confirmed compliant, renamed, or explicitly skipped with a reason.
- **Accuracy**: Prefix categorization must be based on evidence (title, body, labels, linked PR files), never guessed arbitrarily.
- **Ordering**: IDs within each prefix type must be assigned in strict chronological order of issue creation date.
- **Uniqueness**: Zero ID collisions — every assigned ID must be unique and never reuse a reserved ID.
- **Safety**: Only issue titles are changed. Bodies, labels, state, and all other metadata remain untouched.
- **Convention compliance**: Every renamed title must match `^\[[A-Z]{2}-[0-9]{4}\] .{1,60}$` and be ≤ 72 characters.

## Quality Assurance

- [ ] Both naming convention files were read and fully parsed BEFORE any analysis.
- [ ] ALL issues (open + closed) were fetched from the repository — no partial lists.
- [ ] Already-compliant issues were identified and their IDs reserved.
- [ ] The Reserved ID Registry was built before any new IDs were assigned.
- [ ] Every non-compliant issue was categorized using the decision tree with evidence.
- [ ] The `.github/` file classification rule was applied: config files → `TC` or `FR`, never `DC`.
- [ ] Issues were grouped by prefix and sorted by creation date (ascending) within each group.
- [ ] IDs were assigned sequentially starting from highest existing ID + 1 per prefix.
- [ ] No ID was assigned that collides with a reserved (already-existing) ID.
- [ ] No ID was assigned twice across the entire backfill operation.
- [ ] New titles are ≤ 72 characters and match the required pattern.
- [ ] The backfill plan was presented and user confirmation was obtained BEFORE renaming.
- [ ] Each rename was verified by reading back the updated title.
- [ ] Post-rename verification confirmed zero non-compliant titles (excluding skipped issues).
- [ ] Post-rename verification confirmed zero duplicate IDs across all prefixes.
- [ ] Low-confidence categorizations were flagged for user review.
- [ ] Locked or errored issues were logged and reported, not silently skipped.
