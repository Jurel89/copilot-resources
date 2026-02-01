# Getting Started

This repo is a **curated library of GitHub Copilot customization assets**:

- **Agents**: specialized roles with tool access + operating rules
- **Skills**: task-specific, progressively-loaded “toolkits” (docs, scripts, templates)
- **Prompt files**: reusable chat commands (e.g., `/configure-gitignore`)
- **Instruction files**: quality bars and authoring rules (what “good” looks like)

If you’re new, skim the [catalog](../reference/catalog.md) first, then choose one of the integration options below.

---

## Integration options

### Option A (recommended): Use this repo as a shared library

Use this repository as a **single source of truth** and pull assets into other repos when needed.

Common approaches:

- **Copy the specific files** you want into another repository
- **Git submodule / subtree** and reference it during onboarding
- **Internal template repo** that includes (a subset of) these assets

This approach makes it easier to keep assets consistent across many codebases.

### Option B: Vendor assets into a single repository

Copy folders into your project:

- `.github/agents/`
- `.github/instructions/`
- `.github/skills/`
- `.github/prompts/`

This is useful for repos that want fully self-contained Copilot customization.

### Option C: Install skills “personally” (user-wide)

Skill guidelines support several locations (project-level and user-wide). For example:

- Repository skills: `.github/skills/<skill-name>/`
- Personal skills: `~/.github/skills/<skill-name>/`

See [Agent Skills File Guidelines](../../.github/instructions/agent-skills.instructions.md) for the authoritative list.

---

## Quick tour

### Agents

Agents are defined as Markdown files with YAML frontmatter under `.github/agents/*.agent.md`.

In VS Code, agents show up as selectable roles in Copilot Chat (exact UI and availability depends on your Copilot features/version).

### Skills

Skills are folders under `.github/skills/<skill-name>/` that must include `SKILL.md`.

Skills are designed for **progressive loading**:

1. Copilot reads just `name` + `description`
2. If relevant, Copilot loads the body of `SKILL.md`
3. If needed, Copilot pulls additional bundled resources (scripts/templates/references)

### Prompts

Prompts are reusable slash commands stored in `.github/prompts/*.prompt.md`.

### Instructions

Instruction files (`*.instructions.md`) define how to author and maintain the assets in this repo.

---

## First useful workflow

Try the prompt file:

- `.github/prompts/configure-gitignore.prompt.md`

It provides a repeatable checklist for adding ignore rules that keep “internal” Copilot assets out of product repos (when you’re using a shared library approach).

---

## Next steps

- Browse the [catalog](../reference/catalog.md)
- Read the [customization model](../architecture/copilot-customization-model.md)
- If you want to add new assets, follow [authoring guidance](authoring.md)
