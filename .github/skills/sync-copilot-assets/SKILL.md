---
name: sync-copilot-assets
description: 'Synchronize GitHub Copilot customization assets (agents, instructions, prompts, skills) from a central repository to multiple target workspaces. Use when you need to distribute or update Copilot assets across projects. Keywords: sync, distribute, copy, propagate, assets, agents, instructions, prompts, skills, repositories.'
license: Complete terms in LICENSE.txt
---

# Sync Copilot Assets

Synchronize GitHub Copilot customization assets from this repository to multiple target repositories using a cross-platform Node.js script.

## When to Use This Skill

- You want to **distribute** Copilot assets to multiple projects
- You need to **update** existing assets across workspaces
- You're **maintaining** a central repository of Copilot customizations
- Keywords: sync, distribute, copy, propagate, assets, agents, instructions, prompts, skills

## ⚠️ Critical Warning

**This sync performs FULL OVERWRITE synchronization.** Files in target repositories that don't exist in source WILL BE DELETED. Always run dry-run first.

## Prerequisites

- **Node.js 18.x or later** (check with `node --version`)
- No additional dependencies or npm packages needed
- Works on Windows, macOS, and Linux

## Configuration

Before syncing, configure your target repositories in `sync-targets.txt` at the repository root:

```txt
# One absolute path per line
/Users/username/projects/project-alpha
/Users/username/projects/project-beta
```

See [sync-targets-example.txt](./references/sync-targets-example.txt) for a complete example.

## Step-by-Step Workflow

### Step 1: Verify Configuration

Check that target repositories are configured:

```bash
cat sync-targets.txt
```

If empty or only comments, add target paths first.

### Step 2: Preview Changes (Dry-Run)

**ALWAYS** run dry-run first:

```bash
node .github/skills/sync-copilot-assets/scripts/sync-copilot-assets.mjs --dry-run
```

Review the output to see what would be added, updated, or deleted.

### Step 3: Execute Sync

After confirming the dry-run output, run the actual sync:

```bash
node .github/skills/sync-copilot-assets/scripts/sync-copilot-assets.mjs
```

### Step 4: Commit Changes in Targets

After syncing, commit changes in each target repository:

```bash
cd /path/to/target-repo
git add .github/
git commit -m "chore: sync Copilot assets from copilot-resources"
git push
```

## Script Options

| Flag | Description |
|------|-------------|
| `-n`, `--dry-run` | Preview changes without modifying any files |
| `-q`, `--quiet` | Suppress progress output (errors still shown) |
| `-h`, `--help` | Display detailed usage information |
| `-v`, `--version` | Show script version |

## Synced Asset Directories

| Directory | Contents |
|-----------|----------|
| `.github/agents/` | Agent definition files (`.agent.md`) |
| `.github/instructions/` | Instruction files (`.instructions.md`) |
| `.github/prompts/` | Prompt files (`.prompt.md`) |
| `.github/skills/` | Skill folders with resources |

## Output Indicators

| Symbol | Meaning |
|--------|---------|
| `+` | File added (new) |
| `↻` | File updated (overwritten) |
| `-` | File deleted (removed from target) |

## Exit Codes

| Code | Meaning |
|------|---------|
| `0` | All targets synced successfully |
| `1` | Partial success (some targets failed/skipped) |
| `2` | Critical failure (missing source, invalid config) |

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| "Configuration file not found" | `sync-targets.txt` missing | Create the file with target paths |
| "Target path does not exist" | Repo not cloned or wrong path | Verify path exists; clone repo if needed |
| "Permission denied" | No write access to target | Check filesystem permissions |
| "Node.js X.x required" | Node.js version too old | Update Node.js to 18.x or later |
| "Source directories not found" | Running from wrong directory | Run from repository root |

### Recovering from Mistakes

If you accidentally synced unwanted changes:

```bash
cd /path/to/target-repo
git checkout -- .github/
```

Or reset to previous commit:

```bash
git reset --hard HEAD~1
```

## Cross-Platform Notes

Works identically on **Windows**, **macOS**, and **Linux**.

Path formats in `sync-targets.txt`:
- **Windows**: `C:\Users\username\projects\repo` or `C:/Users/username/projects/repo`
- **macOS**: `/Users/username/projects/repo`
- **Linux**: `/home/username/projects/repo`

## References

- [Sync Script](./scripts/sync-copilot-assets.mjs)
- [Example Configuration](./references/sync-targets-example.txt)
