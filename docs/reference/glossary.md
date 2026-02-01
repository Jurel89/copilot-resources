# Glossary

## Agent

A specialized Copilot “role” defined in a `*.agent.md` file (with YAML frontmatter) that changes behavior and tool access.

## Skill

A folder containing a `SKILL.md` and optional bundled resources (scripts, templates, references). Skills are discoverable via `name` + `description` and loaded on demand.

## Prompt file

A reusable chat command stored as `*.prompt.md` (often invoked as a slash command) with a structured workflow and output expectations.

## Instruction file

A `*.instructions.md` file that applies guidelines to target files via `applyTo` globs.

## Progressive loading

A pattern where only lightweight metadata is always scanned, with deeper content loaded only when relevant.

## Handoff

An agent feature that suggests “next step” buttons to switch to another agent with an optional prefilled prompt.

## MCP server

Model Context Protocol server: provides external tools (e.g., GitHub, Azure, etc.) that an agent can call.
