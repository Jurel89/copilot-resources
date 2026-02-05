---
description: 'Synchronize repository GitHub labels with the canonical label taxonomy — creates missing labels, removes unlisted ones'
name: 'sync-labels'
agent: 'The GitChaos Organizer'
tools: ['execute', 'read', 'search', 'todo']
argument-hint: 'Optional: specific label namespace to sync (e.g., type, area, priority). Leave empty to sync all.'
---

# Sync Repository Labels with Canonical Taxonomy

## Mission

Read the **canonical label taxonomy** defined in `.github/skills/github-labels/references/labels.yml`, compare it against the labels that **currently exist** in the repository, then **create every missing label** and **delete every label not present in the taxonomy** — producing a repository whose labels match the taxonomy exactly.

---

## Scope & Preconditions

- **Source of truth**: `.github/skills/github-labels/references/labels.yml` — this file defines the ONLY allowed labels.
- **Target**: The current GitHub repository (determined by the working directory).
- **Destructive operations**: This prompt WILL delete labels that exist in the repo but are absent from the taxonomy. Orphaned labels on existing issues will be silently removed by GitHub.
- **Idempotent**: Running this prompt multiple times produces the same end state.
- **Scope filter**: If the user provides a namespace argument (e.g., `type`, `area`, `priority`), restrict operations to labels in that namespace only and leave all other labels untouched.

---

## Workflow

Execute all phases **sequentially**. Do NOT skip any step.

### Phase 1: Load the Canonical Taxonomy

1. Read the full contents of `.github/skills/github-labels/references/labels.yml`.
2. Parse every label entry from all `groups[].labels[]`.
3. For each label, extract exactly three fields:
   - `name` — the label identifier (e.g., `type:bug`)
   - `color` — the hex color **without** the `#` prefix (e.g., `d73a4a`)
   - `description` — the full description text (truncate to 100 characters for the GitHub API limit)
4. Build a **taxonomy map** keyed by label name containing color and description.
5. Count the total labels parsed and report: _"Taxonomy loaded: N labels across M groups."_

### Phase 2: Fetch Existing Repository Labels

1. List every label currently in the repository:

   ```bash
   gh label list --limit 200 --json name,color,description
   ```

2. Build a **repo map** keyed by label name containing color and description.
3. Count the total labels fetched and report: _"Repository has N existing labels."_

### Phase 3: Compute the Diff

Compare the two maps and classify every label into exactly one category:

| Category | Condition | Action |
|----------|-----------|--------|
| **Missing** | In taxonomy, NOT in repo | Create |
| **Orphaned** | In repo, NOT in taxonomy | Delete |
| **Mismatched** | In both, but color OR description differs | Update |
| **Synced** | In both, color AND description match | Skip |

If the user provided a namespace filter, only process labels whose name starts with that namespace prefix (e.g., `type:` for namespace `type`). Labels outside the filter remain untouched and are excluded from the orphan list.

Present a **summary table** before proceeding:

```
## Label Sync Plan

| Action  | Count | Labels |
|---------|-------|--------|
| Create  | X     | label1, label2, ... |
| Delete  | Y     | label3, label4, ... |
| Update  | Z     | label5, label6, ... |
| Synced  | W     | (no action needed)  |
```

### Phase 4: Execute Sync Operations

#### 4.1 Create Missing Labels

For each missing label, run:

```bash
gh label create "LABEL_NAME" --color "HEX_COLOR" --description "DESCRIPTION"
```

- Use the exact `name`, `color`, and truncated `description` from the taxonomy.
- Verify creation succeeded before moving to the next label.
- If creation fails (e.g., label was created concurrently), log the error and continue.

#### 4.2 Update Mismatched Labels

For each mismatched label, run:

```bash
gh label edit "LABEL_NAME" --color "HEX_COLOR" --description "DESCRIPTION"
```

- Only update the fields that differ (color, description, or both).
- Verify the update succeeded.

#### 4.3 Delete Orphaned Labels

For each orphaned label, run:

```bash
gh label delete "LABEL_NAME" --yes
```

- The `--yes` flag skips confirmation.
- Log each deletion.
- If deletion fails (e.g., label already removed), log and continue.

### Phase 5: Verification

After all operations complete:

1. Re-fetch the repository labels:

   ```bash
   gh label list --limit 200 --json name,color,description
   ```

2. Compare the updated repo labels against the taxonomy.
3. Confirm **zero missing** and **zero orphaned** labels remain.
4. If discrepancies exist, report them and attempt to fix.

### Phase 6: Final Report

Present a structured summary:

```
## Label Sync Complete ✅

**Repository**: <owner>/<repo>
**Taxonomy version**: <version from labels.yml>
**Date**: <current date>

### Results

| Metric         | Count |
|----------------|-------|
| Created        | X     |
| Updated        | Z     |
| Deleted        | Y     |
| Already synced | W     |
| Total labels   | T     |

### Created Labels
- `label1` (color: #HEX)
- `label2` (color: #HEX)

### Updated Labels
- `label3` — color: OLD → NEW
- `label4` — description updated

### Deleted Labels
- `label5` (was orphaned)
- `label6` (was orphaned)

### Verification
✅ All taxonomy labels present in repository
✅ No orphaned labels remain
```

---

## Output Expectations

- **Format**: Structured Markdown report in chat.
- **Completeness**: Every label in the taxonomy must exist in the repo after execution. Every label in the repo must exist in the taxonomy.
- **Accuracy**: Colors and descriptions must match the taxonomy exactly (descriptions truncated to 100 chars).

## Quality Assurance

- [ ] Taxonomy file was read and fully parsed before any operation.
- [ ] Existing labels were fetched from the repository before any create/delete.
- [ ] No label was created without first confirming it does not already exist.
- [ ] No label was deleted without first confirming it is absent from the taxonomy.
- [ ] Namespace filter (if provided) was correctly applied to both create and delete operations.
- [ ] Final verification confirms zero drift between taxonomy and repository.
- [ ] Destructive operations (delete) used the `--yes` flag to avoid hanging prompts.
