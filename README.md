# Copilot Resources (provisional)

A lightweight collection of reusable resources for **GitHub Copilot** and agent-driven workflows.
This repo is primarily a library of:

- **Custom agents** (role definitions)
- **Instruction files** (authoring rules / quality bars)
- **Agent “skills”** (repeatable, documented capabilities)

## Repository layout

| Path | What it contains |
| --- | --- |
| [.github/agents/](.github/agents/) | Custom agent definitions (`*.agent.md`) |
| [.github/instructions/](.github/instructions/) | Writing guidelines for agents, instructions, prompts, and skills |
| [.github/skills/](.github/skills/) | Skill packs (each skill has its own folder) |

## How to use

Common ways to consume these resources:

1. **Copy the pieces you want** into another repo that’s using Copilot custom instructions/agents.
2. **Fork this repo** and tailor the agents/instructions/skills to your org’s conventions.
3. **Vendor as a submodule/subtree** and reference the files from your workflow.

If you’re new to this repo, start here:

- Agents: browse [.github/agents/](.github/agents/)
- Authoring rules: browse [.github/instructions/](.github/instructions/)
- Skills catalog: browse [.github/skills/](.github/skills/)

## Conventions

- Agent files end in `*.agent.md`.
- Skills live under `.github/skills/<skill-name>/` and typically include a `SKILL.md`.
- Instruction files end in `*.instructions.md`.

## Status

This README is **provisional** and will evolve as the library grows.

## License

See [LICENSE](LICENSE).
