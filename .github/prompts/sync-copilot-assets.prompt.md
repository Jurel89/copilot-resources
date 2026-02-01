---
description: 'Sync Copilot agents, instructions, prompts, and skills from copilot-resources to target repositories'
name: 'sync-copilot-assets'
agent: 'agent'
argument-hint: 'Optional: --dry-run to preview changes first'
---

# Sync Copilot Assets

Follow the instructions in the **sync-copilot-assets** skill located at `.github/skills/sync-copilot-assets/SKILL.md`.

## Quick Reference

### Step 1: Verify Configuration

```bash
cat sync-targets.txt
```

### Step 2: Dry-Run (Always First)

```bash
node .github/skills/sync-copilot-assets/scripts/sync-copilot-assets.mjs --dry-run
```

### Step 3: Execute Sync (After User Confirms)

```bash
node .github/skills/sync-copilot-assets/scripts/sync-copilot-assets.mjs
```

### Step 4: Report Results

Summarize successes/failures and remind user to commit changes in target repositories.

## Full Documentation

See [SKILL.md](../skills/sync-copilot-assets/SKILL.md) for complete documentation including:
- All script options
- Troubleshooting guide
- Cross-platform notes
- Recovery procedures

### Automated Verification

```bash
# Check sync script exit code
echo $?
# 0 = all success, 1 = partial success, 2 = critical failure
```

### Manual Verification Checklist

- [ ] Script completed without critical errors
- [ ] Summary shows expected number of successful syncs
- [ ] For key targets, spot-check that files exist:

```bash
# Example: verify a target has the assets
ls -la /path/to/target-repo/.github/agents/
ls -la /path/to/target-repo/.github/instructions/
ls -la /path/to/target-repo/.github/prompts/
ls -la /path/to/target-repo/.github/skills/
```

- [ ] In target repos, run `git status` to see changes:

```bash
cd /path/to/target-repo
git status
```

- [ ] Review diffs if needed:

```bash
git diff .github/
```

### Commit Changes in Target Repos

After verification, commit the synced changes in each target repository:

```bash
cd /path/to/target-repo
git add .github/agents/ .github/instructions/ .github/prompts/ .github/skills/
git commit -m "chore: sync Copilot assets from copilot-resources"
git push
```

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| "Configuration file not found" | `sync-targets.txt` missing | Create the file with target paths |
| "Target path does not exist" | Repo not cloned or wrong path | Verify path exists; clone repo if needed |
| "Permission denied" | No write access to target | Check filesystem permissions |
| "Node.js X.x or later is required" | Node.js version too old | Update Node.js to 18.x or later |
| "Source directories not found" | Running from wrong directory | Run from copilot-resources repo root |

### Getting Help

```bash
# Show detailed usage information
node scripts/sync-copilot-assets.mjs --help

# Check script version
node scripts/sync-copilot-assets.mjs --version

# Check Node.js version
node --version
```

### Recovering from Mistakes

If you accidentally synced unwanted changes:

1. In the target repository, use `git checkout` to restore:
   ```bash
   cd /path/to/target-repo
   git checkout -- .github/agents/ .github/instructions/ .github/prompts/ .github/skills/
   ```

2. Or reset to a previous commit:
   ```bash
   git reset --hard HEAD~1
   ```

## Output Expectations

### Console Output

The script produces structured output:

```
╔════════════════════════════════════════════════════════════════╗
║          Copilot Assets Sync                                   ║
╚════════════════════════════════════════════════════════════════╝

  Started: 2026-02-01 10:30:00
  Source:  /Users/username/copilot-resources

────────────────────────────────────────────────────────────────
Found 3 target(s) to sync

  → Syncing to: project-alpha
    /Users/username/projects/project-alpha
    .github/agents/
      + new-agent.agent.md
      ↻ existing-agent.agent.md
    .github/instructions/
      ↻ coding.instructions.md
    ✅ Success (+1 ↻2 -0)

  → Syncing to: project-beta
    /Users/username/projects/project-beta
    ✅ Success (+0 ↻4 -1)

────────────────────────────────────────────────────────────────
Summary
────────────────────────────────────────────────────────────────

  Total targets:  3
  Succeeded:      3
  Failed:         0
  Skipped:        0

  Elapsed time:   2s

✅ All targets synced successfully!
```

### File Change Indicators

| Symbol | Meaning |
|--------|---------|
| `+` | File added (new file) |
| `↻` | File updated (overwritten) |
| `-` | File deleted (removed from target) |

### Exit Codes

| Code | Meaning |
|------|---------|
| `0` | All targets synced successfully |
| `1` | Partial success (some targets failed or skipped) |
| `2` | Critical failure (missing source, invalid config, Node.js version) |

## Quality Assurance

- [ ] Dry-run executed before actual sync
- [ ] No unexpected deletions in dry-run output
- [ ] All expected targets processed
- [ ] Exit code is 0 (full success)
- [ ] Spot-checked at least one target repository
- [ ] Changes committed and pushed in target repositories

## Cross-Platform Notes

This script works identically on **Windows**, **macOS**, and **Linux**:

- **Windows**: Use Command Prompt, PowerShell, or Git Bash
- **macOS/Linux**: Use any terminal

Path formats in `sync-targets.txt`:
- Windows: `C:\Users\username\projects\repo` or `C:/Users/username/projects/repo`
- macOS: `/Users/username/projects/repo`
- Linux: `/home/username/projects/repo`

## Related Resources

- [Sync Script Source](../../scripts/sync-copilot-assets.mjs)
- [Legacy Shell Script (deprecated)](../../scripts/sync-copilot-assets.sh)
- [Target Configuration](../../sync-targets.txt)
- [Copilot Assets Sync TRD](../../docs/internal/requirements/2026-02-01-copilot-assets-sync-trd.md)
- [Cross-Platform Sync TRD](../../docs/internal/requirements/2026-02-01-cross-platform-sync-trd.md)
