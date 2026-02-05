---
description: 'CRITICAL: Classification rules for files in .github/ directory - prevents misclassifying AI configuration files as documentation. Apply these rules BEFORE assigning issue types or labels to .github changes.'
applyTo: '.github/**/*.md, .github/**/*.yml, .github/**/*.yaml'
---

# GitHub Configuration File Classification

## ⛔ CRITICAL RULE

Files in `.github/` that configure AI tooling (Copilot agents, skills, instructions, prompts) are **CONFIGURATION**, not documentation.

**NEVER use `type:docs` or `DC-####` for these files.**

## Why This Matters

LLMs have a strong prior: "`.md` file extension → documentation". This heuristic is **WRONG** for AI configuration files. These files are:

- **Executable configuration** that changes AI behavior
- **Tooling/infrastructure** that powers development workflows  
- **NOT human-readable documentation** like guides or READMEs

Misclassification causes:
- Incorrect issue prefixes (`DC-####` instead of `TC-####`)
- Wrong labels (`type:docs` instead of `type:chore`)
- Broken traceability and reporting

## Classification Matrix

| File Pattern | What It Is | Issue Type | Type Label | Area Label | NEVER |
|--------------|------------|------------|------------|------------|-------|
| `*.agent.md` | AI agent definition | TC or FR | `type:chore` or `type:feature` | `area:ai` | `type:docs`, `area:docs`, `DC-####` |
| `*.instructions.md` | Copilot instruction rules | TC or FR | `type:chore` or `type:feature` | `area:ai` | `type:docs`, `area:docs`, `DC-####` |
| `SKILL.md` | Copilot skill definition | TC or FR | `type:chore` or `type:feature` | `area:ai` | `type:docs`, `area:docs`, `DC-####` |
| `*.prompt.md` | Reusable prompt template | TC or FR | `type:chore` or `type:feature` | `area:ai` | `type:docs`, `area:docs`, `DC-####` |
| `workflows/*.yml` | CI/CD pipeline | IN | `type:ci` | `area:infra` | `type:docs`, `area:docs`, `DC-####` |
| `actions/*` | GitHub Action config | IN | `type:ci` | `area:infra` | `type:docs`, `area:docs`, `DC-####` |
| `labels.yml` | Label taxonomy config | TC | `type:chore` | `area:ai` | `type:docs`, `area:docs`, `DC-####` |
| `issue-types.yml` | Issue type taxonomy | TC | `type:chore` | `area:ai` | `type:docs`, `area:docs`, `DC-####` |

## When TO Use Documentation Type

Use `DC-####`, `type:docs`, and `area:docs` **ONLY** for:

| Pattern | Why It's Documentation | Labels |
|---------|------------------------|--------|
| `docs/**/*.md` | Actual documentation directory | `type:docs`, `area:docs` |
| `README.md` | Project/package overview for humans | `type:docs`, `area:docs` |
| `CHANGELOG.md` | Release notes for humans | `type:docs`, `area:docs` |
| `CONTRIBUTING.md` | Contributor guide for humans | `type:docs`, `area:docs` |
| `SECURITY.md` | Security policy for humans | `type:docs`, `area:docs` |
| `CODE_OF_CONDUCT.md` | Community guidelines for humans | `type:docs`, `area:docs` |
| API reference docs | Technical docs for humans | `type:docs`, `area:docs` |

## Decision Flowchart

```
Is the file in .github/?
├─ YES → Is it *.agent.md, *.instructions.md, SKILL.md, or *.prompt.md?
│        ├─ YES → Use TC-#### or FR-#### (NEVER DC-####)
│        └─ NO → Is it in workflows/ or actions/?
│                 ├─ YES → Use IN-#### (Infrastructure)
│                 └─ NO → Is it a config file (.yml, .yaml)?
│                          ├─ YES → Use TC-#### (Technical Chore)
│                          └─ NO → Evaluate case by case
└─ NO → Is it in docs/ or a standard doc file (README, CHANGELOG)?
         ├─ YES → Use DC-#### (Documentation) ✓
         └─ NO → Evaluate based on content
```

## Examples

### ✅ CORRECT Classification

| Change | Issue ID | Labels | Why |
|--------|----------|--------|-----|
| Update `the-researcher.agent.md` | `TC-0045` | `type:chore`, `area:ai` | AI agent config |
| Add new `code-review.prompt.md` | `FR-0078` | `type:feature`, `area:ai` | New AI capability |
| Fix typo in `python.instructions.md` | `TC-0046` | `type:chore`, `area:ai` | Config maintenance |
| Refactor `github-labels/SKILL.md` | `TC-0047` | `type:chore`, `area:ai` | Skill improvement |
| Add `ci.yml` workflow | `IN-0023` | `type:ci`, `area:infra` | CI/CD infrastructure |
| Update `docs/guides/getting-started.md` | `DC-0035` | `type:docs`, `area:docs` | Actual documentation |
| Add API examples to `README.md` | `DC-0036` | `type:docs`, `area:docs` | Actual documentation |

### ❌ INCORRECT Classification (Don't Do This)

| Change | WRONG | CORRECT | Why |
|--------|-------|---------|-----|
| Update `the-researcher.agent.md` | `DC-0045`, `type:docs`, `area:docs` | `TC-0045`, `type:chore`, `area:ai` | Agent files are config |
| Add `validation.instructions.md` | `DC-0046`, `type:docs`, `area:docs` | `FR-0079`, `type:feature`, `area:ai` | Instruction files are config |
| Refactor `SKILL.md` | `DC-0047`, `type:docs`, `area:docs` | `TC-0048`, `type:chore`, `area:ai` | Skills are config |

## Enforcement

When creating issues or applying labels for `.github/` files:

1. **STOP** before automatically using "documentation"
2. **CHECK** this classification matrix for BOTH type AND area
3. **APPLY** the correct `type:*` label based on file pattern
4. **APPLY** the correct `area:*` label based on file location (`area:ai` for `.github/` config, `area:docs` for actual docs)
5. **VERIFY** you haven't used `DC-####`, `type:docs`, or `area:docs` for config files

## Related Skills

- `github-labels` — Canonical label taxonomy
- `issue-naming-conventions` — Issue ID prefixes and naming rules
- `gh-cli` — GitHub CLI commands for issues and PRs
