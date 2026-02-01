---
description: 'Configure .gitignore to exclude internal documentation and Copilot customization files from version control'
name: 'configure-gitignore'
agent: 'agent'
tools: ['readFile', 'editFiles']
argument-hint: 'Optional: specify additional paths to ignore'
---

# Configure Gitignore for Internal Assets

## Mission

Configure the project's `.gitignore` file to exclude internal documentation and GitHub Copilot customization files from version control. This ensures that internal assets remain private and that Copilot artifacts (agents, instructions, skills, prompts) are not committed to project repositories—they should only exist in the central copilot-resources repository.

## Scope & Preconditions

### When to Use This Prompt

- At the beginning of a new project setup
- When onboarding an existing project to use shared Copilot customizations
- After cloning a repository that needs internal asset exclusions

### Prerequisites

- The workspace must be a Git repository (or will become one)
- User has write access to the repository root

## Inputs

- `${file}`: Current file context (if any, used to identify project root)
- `${workspaceFolder}`: The root of the workspace where `.gitignore` should be configured
- User may optionally specify additional paths to ignore

## Workflow

### 1. Locate or Create .gitignore

1. Check if `.gitignore` exists at the workspace root
2. If it does not exist, create a new `.gitignore` file
3. If it exists, read its current contents to avoid duplicates

### 2. Add Internal Documentation Exclusions

Add the following patterns if not already present:

```gitignore
# Internal documentation (not for public repos)
docs/internal/
**/docs/internal/
```

### 3. Add Copilot Customization Exclusions

Add patterns to exclude Copilot artifacts while preserving GitHub workflows:

```gitignore
# GitHub Copilot customization files
# These should only exist in the central copilot-resources repository
.github/agents/
.github/instructions/
.github/skills/
.github/prompts/
.github/copilot-instructions.md

# Keep GitHub workflows (negate the exclusion)
!.github/workflows/
```

### 4. Add Claude/AI Configuration Exclusions (if applicable)

```gitignore
# Claude AI configuration (centralized elsewhere)
.claude/
CLAUDE.md
```

### 5. Preserve Existing Content

- Do NOT remove or modify existing gitignore rules
- Add new rules in a clearly marked section
- Maintain proper formatting with blank lines between sections

### 6. Validate the Configuration

After editing, verify:
- The file is valid gitignore syntax
- Workflow files will NOT be ignored (test with `!.github/workflows/`)
- All specified internal paths are covered

## Output Expectations

### File Location

`${workspaceFolder}/.gitignore`

### Expected Content Block

The following block should be added (or merged) into the `.gitignore`:

```gitignore
# =============================================================================
# Internal & Copilot Assets
# These files should not be committed to this repository.
# Copilot customizations are managed in the central copilot-resources repo.
# =============================================================================

# Internal documentation
docs/internal/
**/docs/internal/

# GitHub Copilot customization files
.github/agents/
.github/instructions/
.github/skills/
.github/prompts/
.github/copilot-instructions.md

# Claude AI configuration
.claude/
CLAUDE.md

# Ensure workflows are NOT ignored
!.github/workflows/
```

### Success Criteria

- [ ] `.gitignore` file exists at workspace root
- [ ] Internal documentation paths are excluded
- [ ] Copilot customization directories are excluded
- [ ] GitHub workflows directory is explicitly preserved (negation pattern)
- [ ] No duplicate rules exist
- [ ] Existing gitignore content is preserved

## Quality Assurance

### Manual Verification

After running this prompt:

1. **Check file exists**: `ls -la .gitignore`
2. **Verify content**: `cat .gitignore | grep -A 20 "Internal & Copilot"`
3. **Test workflow preservation**: `git check-ignore .github/workflows/ci.yml` (should return nothing/not ignored)
4. **Test agent exclusion**: `git check-ignore .github/agents/test.agent.md` (should return the path as ignored)

### Common Issues

| Issue | Solution |
|-------|----------|
| Workflows being ignored | Ensure `!.github/workflows/` comes AFTER `.github/` patterns |
| Patterns not working | Check for typos, ensure no trailing spaces |
| Duplicate entries | Review file and remove duplicates manually |

## Notes

- This configuration assumes Copilot customizations are symlinked or copied from a central repository during development
- The negation pattern for workflows (`!`) must appear after any broader `.github/` exclusions to work correctly
- If the project needs other `.github/` contents (like `CODEOWNERS`, `FUNDING.yml`), add specific negation patterns for those files
