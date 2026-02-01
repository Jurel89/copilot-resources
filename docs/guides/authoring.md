# Authoring New Assets

This repo treats Copilot customization artifacts as **first-class, versioned assets**.

Use the instruction files under `.github/instructions/` as the quality bar for authoring and reviews.

## What to create (and when)

| You need… | Create… | Lives in… |
| --- | --- | --- |
| A reusable “role” that changes behavior/tooling | Agent (`*.agent.md`) | `.github/agents/` |
| An on-demand toolkit with docs/scripts/templates | Skill folder + `SKILL.md` | `.github/skills/<skill-name>/` |
| A repeatable slash command to run in chat | Prompt (`*.prompt.md`) | `.github/prompts/` |
| A quality bar / convention applied by glob | Instructions (`*.instructions.md`) | `.github/instructions/` |

## File-by-file guidance

### Agents

- Follow: `.github/instructions/agents.instructions.md`
- Key concepts: YAML frontmatter, tool allowlists, `infer`, and `handoffs`
- Goal: agents should be **opinionated and reliable**, not generic

### Skills

- Follow: `.github/instructions/agent-skills.instructions.md`
- Key concept: the `description` field is what enables discovery (WHAT + WHEN + KEYWORDS)
- Keep the main `SKILL.md` under ~500 lines; move deep detail into `references/`

### Prompt files

- Follow: `.github/instructions/prompt.instructions.md`
- Include: mission, inputs, workflow steps, output expectations, QA checklist

### Instruction files

- Follow: `.github/instructions/instructions.instructions.md`
- Use `applyTo` globs narrowly; include good/bad examples when helpful

## Review checklist (repo-level)

- The asset matches the required naming conventions (kebab-case)
- Frontmatter is valid and complete
- Links are relative and work inside the repo
- The artifact is discoverable (good descriptions, “when to use” guidance)
- No secrets or credentials are embedded in examples
