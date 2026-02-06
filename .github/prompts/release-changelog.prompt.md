---
description: 'Generate or update CHANGELOG.md from commits since last tag, bump version via issue/branch/PR workflow, then tag and publish a GitHub Release — with dry-run support'
name: 'release-changelog'
agent: The GitChaos Organizer
tools: ['execute', 'read', 'edit', 'search', 'todo']
argument-hint: 'Bump type: major | minor | patch (required). Add --dry-run to preview without committing.'
---

# Release Changelog

## Mission

Generate a **production-grade changelog entry**, bump the project version, create a git tag, and publish a GitHub Release — all following the **[Keep a Changelog v1.1.0](https://keepachangelog.com/en/1.1.0/)** convention and **[Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html)**.

All file changes are made through a **proper development workflow**: GitHub issue → release branch → pull request → CI verification → merge → tag → release. No direct commits to main.

This prompt supports a **dry-run mode** that previews every change without modifying the repository.

---

## Inputs

- **`${input:bumpType:major | minor | patch}`** — **Required**. The semver component to increment.
  - `major` — Breaking changes (incompatible API changes)
  - `minor` — New functionality (backward-compatible)
  - `patch` — Bug fixes (backward-compatible)
- **`--dry-run`** flag (detected from user input) — If present, preview all changes but **do not** write files, commit, tag, push, or create a release. Print what *would* happen instead.

If the bump type is missing or unclear, **stop and ask the user** before proceeding.

---

## Scope & Preconditions

Before starting, verify ALL of the following. If any check fails, **stop and report the issue**.

```bash
# 1. Inside a git repository
git rev-parse --is-inside-work-tree

# 2. On the expected release branch (main or master)
git branch --show-current

# 3. Working tree is clean (no uncommitted changes)
git status --porcelain

# 4. Authenticated with GitHub CLI
gh auth status

# 5. Remote is reachable
git remote -v
```

**Failure behavior:**
- Not a git repo → Stop: "This directory is not a git repository."
- Dirty working tree → Stop: "You have uncommitted changes. Commit or stash them before releasing."
- Not on main/master → Stop: "You must be on the main (or master) branch to start a release. The workflow will create a release branch from here."
- `gh` not authenticated → Stop: "Run `gh auth login` first."

---

## Workflow

### Phase 1: Discovery

#### Step 1.1 — Find the latest tag

```bash
# Get the most recent semver tag
git describe --tags --abbrev=0 --match "v*" 2>/dev/null || echo "NO_TAG"

# If no tag exists, use the root commit as the base
git rev-list --max-parents=0 HEAD
```

- If no tag exists, treat the entire commit history as the first release and inform the user.

#### Step 1.2 — Read current version

Check for a `VERSION` file in the project root:

```bash
cat VERSION 2>/dev/null || echo "NO_VERSION_FILE"
```

- If `VERSION` exists, its value is the **current version**.
- If `VERSION` does not exist, derive the current version from the latest git tag (strip the `v` prefix).
- If neither exists, assume `0.0.0` and inform the user.

#### Step 1.3 — Compute the new version

Apply the requested bump type to the current version:

| Current | Bump Type | New Version |
|---------|-----------|-------------|
| 1.2.3   | patch     | 1.2.4       |
| 1.2.3   | minor     | 1.3.0       |
| 1.2.3   | major     | 2.0.0       |

**Ask the user to confirm the new version** before proceeding (display: `Current: X.Y.Z → New: A.B.C`).

---

### Phase 2: Commit Analysis

#### Step 2.1 — Collect commits since last tag

```bash
# If a tag exists
git log <LAST_TAG>..HEAD --format="%H %s" --no-merges

# If no tag exists
git log --format="%H %s" --no-merges
```

#### Step 2.2 — Collect file change statistics

```bash
# Summary of files changed
git diff --stat <LAST_TAG>..HEAD

# Number of contributors
git log <LAST_TAG>..HEAD --format="%aN" | sort -u
```

#### Step 2.3 — Categorize commits into Keep a Changelog sections

Map each commit to **exactly one** changelog section based on its conventional commit type prefix. If a commit does not follow conventional commits format, analyze its message and diff to determine the best category.

**Mapping table (Conventional Commits → Keep a Changelog):**

| Conventional Commit Type | Changelog Section | Description |
|--------------------------|-------------------|-------------|
| `feat`                   | **Added**         | New features |
| `fix`                    | **Fixed**         | Bug fixes |
| `refactor`, `perf`       | **Changed**       | Changes in existing functionality |
| `deprecate`              | **Deprecated**    | Soon-to-be removed features |
| `remove`                 | **Removed**       | Removed features |
| `security`               | **Security**      | Vulnerability fixes |
| `docs`, `style`, `test`, `build`, `ci`, `chore` | *(Evaluate individually)* | Include only if notable to end users; skip internal-only changes |

**Guidelines for categorization:**
- Changelogs are **for humans, not machines** — write entries that end users and contributors can understand
- Each entry should describe the **user-facing impact**, not the implementation detail
- Combine related commits into a single entry when they address the same feature or fix
- Omit trivial changes (typo fixes, whitespace, internal CI tweaks) unless they are the only changes
- If a commit message references an issue or PR (e.g., `#123`), preserve the reference as a markdown link
- Commits with `BREAKING CHANGE` in the footer or `!` after the type belong in **Changed** with a clear `**BREAKING:**` prefix

**Entry writing rules:**
- Start each entry with a capital letter
- Use present tense, imperative mood (e.g., "Add dark mode support" not "Added dark mode")
- Keep entries concise but informative (one line, no period at the end)
- Reference issues/PRs: `- Add user search endpoint ([#42](../../issues/42))`

---

### Phase 3: Generate Changelog Entry

#### Step 3.1 — Format the new entry

Follow the **Keep a Changelog v1.1.0** format strictly:

```markdown
## [X.Y.Z] - YYYY-MM-DD

### Added

- Entry for new feature ([#PR](link))

### Changed

- Entry for changed behavior

### Deprecated

- Entry for deprecated feature

### Removed

- Entry for removed feature

### Fixed

- Entry for bug fix ([#ISSUE](link))

### Security

- Entry for security fix
```

**Formatting rules:**
- Date format: **ISO 8601** (`YYYY-MM-DD`), using today's date
- Only include sections that have entries (omit empty sections)
- Order sections: Added → Changed → Deprecated → Removed → Fixed → Security
- Version heading is an `##` (H2) with the version in square brackets
- Each section heading is an `###` (H3)
- Each entry is a `- ` bullet point

#### Step 3.2 — Present preview to the user

Display the **complete generated changelog entry** to the user and ask for confirmation:

```
📋 Changelog Preview for v{X.Y.Z}
══════════════════════════════════

{formatted changelog entry}

══════════════════════════════════
Commits analyzed: {count}
Contributors: {names}

Does this look correct? (yes/edit/cancel)
```

- **yes** → Proceed to Phase 4
- **edit** → Ask the user what to change, regenerate, and re-confirm
- **cancel** → Abort the entire workflow

---

### Phase 4: Issue & Branch Setup

> **DRY-RUN CHECK**: If `--dry-run` was specified, print everything from Phase 4 onward as "Would do:" prefixed actions and **stop here**. Do not modify any files, do not run git commands that change state. Skip to the [Dry-Run Output Format](#dry-run-output-format) section.

#### Step 4.1 — Create a GitHub issue for the release

```bash
gh issue create \
  --title "chore(release): v{X.Y.Z}" \
  --body "## Release v{X.Y.Z}

Update CHANGELOG.md and VERSION for release v{X.Y.Z}.

### Changes included

{summary list of changelog sections and entry counts}

### Checklist

- [ ] CHANGELOG.md updated following Keep a Changelog v1.1.0
- [ ] VERSION file bumped to {X.Y.Z}
- [ ] PR reviewed and merged
- [ ] Git tag v{X.Y.Z} created
- [ ] GitHub Release published" \
  --label "release"
```

**Capture the issue number** from the output for use in subsequent steps.

#### Step 4.2 — Create a release branch

```bash
# Ensure we branch from latest main
git pull origin main

# Create and switch to release branch
git checkout -b release/v{X.Y.Z}
```

---

### Phase 5: Apply Changes

#### Step 5.1 — Update CHANGELOG.md

- If `CHANGELOG.md` exists: Insert the new entry **after the file header** (the `# Changelog` heading and any preamble text) and **before the first existing version entry**
- If `CHANGELOG.md` does not exist: Create it with the standard Keep a Changelog header:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [X.Y.Z] - YYYY-MM-DD

{entries}
```

- Preserve all existing content below the new entry unchanged
- Ensure a single blank line separates the new entry from the previous version entry

#### Step 5.2 — Update VERSION file (if it exists)

- If a `VERSION` file exists, update it to the new version number (just the bare number, no `v` prefix, no trailing newline artifacts)
- If no `VERSION` file exists, skip this step (do not create one unless asked)

#### Step 5.3 — Commit the changes

```bash
git add CHANGELOG.md
git add VERSION 2>/dev/null  # Only if it exists

git commit -m "chore(release): v{X.Y.Z}

Update CHANGELOG.md and VERSION for release v{X.Y.Z}

Closes #{issue-number}"
```

#### Step 5.4 — Push the release branch

```bash
git push -u origin release/v{X.Y.Z}
```

---

### Phase 6: Pull Request & CI

#### Step 6.1 — Create a pull request

```bash
gh pr create \
  --base main \
  --head release/v{X.Y.Z} \
  --title "chore(release): v{X.Y.Z}" \
  --body "## Release v{X.Y.Z}

### Summary

Update CHANGELOG.md and VERSION for release v{X.Y.Z}.

### Changelog Entry

{formatted changelog entry from Phase 3}

### Related Issue

Closes #{issue-number}

### Checklist

- [x] CHANGELOG.md follows Keep a Changelog v1.1.0
- [x] VERSION bumped to {X.Y.Z}
- [x] Conventional commit used
- [x] Issue linked"
```

**Capture the PR number** from the output.

#### Step 6.2 — Wait for CI checks

```bash
# Watch all checks until completion
gh pr checks {PR-number} --watch
```

#### Step 6.3 — Handle CI failures

If any check fails:

1. **Analyze the failure**:
   ```bash
   gh run view {run-id} --log-failed
   ```

2. **Fix if possible** (lint errors, formatting, etc.) on the release branch:
   ```bash
   git add {fixed-files}
   git commit -m "fix(release): address CI feedback for v{X.Y.Z}"
   git push
   ```

3. **Re-run checks**:
   ```bash
   gh run rerun {run-id} --failed
   ```

4. **If unable to fix**: Report the failure to the user and halt. Do not merge a failing PR.

---

### Phase 7: Merge, Tag & Release

#### Step 7.1 — Merge the PR

Once all checks pass:

```bash
gh pr merge {PR-number} --squash --delete-branch
```

#### Step 7.2 — Verify issue closure

```bash
gh issue view {issue-number} --json state,stateReason
```

If the issue was not closed automatically:

```bash
gh issue close {issue-number} --reason completed
```

#### Step 7.3 — Return to main and pull

```bash
git checkout main
git pull origin main
```

#### Step 7.4 — Create git tag

```bash
git tag -a "v{X.Y.Z}" -m "Release v{X.Y.Z}"
git push origin "v{X.Y.Z}"
```

#### Step 7.5 — Create GitHub Release

```bash
gh release create "v{X.Y.Z}" \
  --title "v{X.Y.Z}" \
  --notes "{changelog entry body without the ## heading}" \
  --target main
```

- The release notes should contain **only the section content** (### headings and bullet entries), not the `## [X.Y.Z]` heading or date (GitHub shows those in the release UI)

---

### Phase 8: Verification & Report

#### Step 8.1 — Verify final state

```bash
# On main branch
git branch --show-current

# Tag exists locally
git tag -l "v{X.Y.Z}"

# Working tree is clean
git status --porcelain

# Release exists on GitHub
gh release view "v{X.Y.Z}"

# Release branch was deleted
git branch -a | grep release/v{X.Y.Z} || echo "Branch cleaned up ✅"
```

#### Step 8.2 — Final report

Present a summary to the user:

```
✅ Release v{X.Y.Z} published successfully!
═══════════════════════════════════════════

📋 Issue           #{issue-number} — closed ✅
🌿 Branch          release/v{X.Y.Z} → merged & deleted
🔀 Pull Request    #{PR-number} — squash merged ✅
📄 CHANGELOG.md    Updated with {N} entries
📌 VERSION         {old} → {new}
🏷️  Git Tag         v{X.Y.Z} created and pushed
🚀 GitHub Release  https://github.com/{owner}/{repo}/releases/tag/v{X.Y.Z}

📊 Release Stats
   Commits analyzed: {count}
   Contributors:     {names}
   Sections:         {list of non-empty sections}
```

---

## Dry-Run Output Format

When `--dry-run` is active, present all actions as a preview:

```
🔍 DRY RUN — No changes will be made
═══════════════════════════════════════

📌 Version Bump: {current} → {new} ({bump type})
🏷️  Tag to create: v{X.Y.Z}

📋 Changelog entry that would be added:
───────────────────────────────────────
{formatted changelog entry}
───────────────────────────────────────

📝 Files that would be modified:
   • CHANGELOG.md (insert new entry at top)
   • VERSION (update to {new version})

🔧 Workflow that would execute:
   1. gh issue create --title "chore(release): v{X.Y.Z}" ...
   2. git checkout -b release/v{X.Y.Z}
   3. [Edit CHANGELOG.md and VERSION]
   4. git add CHANGELOG.md VERSION
   5. git commit -m "chore(release): v{X.Y.Z} — Closes #..."
   6. git push -u origin release/v{X.Y.Z}
   7. gh pr create --base main --head release/v{X.Y.Z} ...
   8. gh pr checks {PR} --watch
   9. gh pr merge {PR} --squash --delete-branch
  10. git checkout main && git pull origin main
  11. git tag -a "v{X.Y.Z}" -m "Release v{X.Y.Z}"
  12. git push origin "v{X.Y.Z}"
  13. gh release create "v{X.Y.Z}" --title "v{X.Y.Z}" --notes "..."

✅ Dry run complete. Run again without --dry-run to execute.
```

---

## Error Handling

| Error | Action |
|-------|--------|
| No commits since last tag | Stop: "No new commits found since v{X.Y.Z}. Nothing to release." |
| Tag already exists for computed version | Stop: "Tag v{X.Y.Z} already exists. Verify the bump type or delete the existing tag." |
| Branch `release/v{X.Y.Z}` already exists | Stop: "Release branch already exists. Delete it or choose a different version." |
| CI checks fail | Attempt to fix on the release branch. If unable, report the failure and halt. Do not merge failing PRs. |
| Merge conflicts on PR | Report the conflict details and halt. Ask the user to resolve manually. |
| Push fails | Report the error and suggest `git push` troubleshooting steps. Do not retry automatically. |
| `gh release create` fails | Report the error. The tag and merged PR are still valid — the user can create the release manually via `gh release create`. |
| CHANGELOG.md has unexpected format | Warn the user and ask whether to append at top or abort. Do not silently corrupt the file. |
| Issue creation fails | Report the error and halt. The workflow cannot proceed without a tracking issue. |

---

## Quality Assurance

- [ ] Changelog follows [Keep a Changelog v1.1.0](https://keepachangelog.com/en/1.1.0/) format exactly
- [ ] Version follows [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html)
- [ ] Date uses ISO 8601 format (YYYY-MM-DD)
- [ ] Empty changelog sections are omitted
- [ ] Sections are ordered: Added → Changed → Deprecated → Removed → Fixed → Security
- [ ] Entries are human-readable, not raw commit messages
- [ ] Issue/PR references are preserved as markdown links
- [ ] Breaking changes are clearly marked
- [ ] Existing CHANGELOG.md content is preserved unchanged
- [ ] Dry-run mode produces no side effects
- [ ] User confirmed the changelog preview before any writes
- [ ] GitHub issue created and linked to PR
- [ ] Changes committed to a `release/v{X.Y.Z}` branch, not directly to main
- [ ] PR created, CI checks passed, and PR merged via squash
- [ ] Release branch deleted after merge
- [ ] Issue closed (automatically via `Closes #` or manually)
- [ ] Git tag created on main after merge, matches `v` + version format
- [ ] GitHub Release notes do not duplicate the version heading
- [ ] Final state: on main branch, working tree clean
