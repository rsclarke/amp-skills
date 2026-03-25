---
name: creating-conventional-branches
description: Creates and checks out Git branches that follow Conventional Branch naming. Use when asked to create a new branch from an issue, ticket, or task description.
---

# Creating Conventional Branches

Creates a new Git branch using the [Conventional Branch](https://conventional-branch.github.io/) pattern and verifies the result.

## Prerequisites

- Branch naming context (issue number, ticket number, or short task description)
- Clean git working directory, or explicit user approval to proceed with local changes

## Workflow

### 1. Verify Repository State

```bash
git status --short
git branch --show-current
```

If the working directory is dirty, ask the user before creating the branch.

### 2. Gather Branch Inputs

Collect:
- **Issue or ticket number** (if available)
- **Short description** from the issue title or task summary
- **Type signal** from labels or request language (bug, feature, docs, etc.)

### 3. Select Branch Prefix

Use this mapping:

| Labels / Signals | Prefix |
|------------------|--------|
| `enhancement`, `feature`, `feat` | `feat/` |
| `bug`, `defect` | `fix/` |
| `critical`, `urgent`, `hotfix` | `hotfix/` |
| `documentation`, `docs` | `chore/` |
| `dependencies`, `deps` | `chore/` |
| `chore`, `maintenance` | `chore/` |
| `release` | `release/` |
| (no matching signals) | `feat/` |

### 4. Build the Description Slug

Normalize the description to match the spec:
- Lowercase only
- Keep letters, numbers, and hyphens
- Replace spaces/underscores with hyphens
- Collapse repeated hyphens
- Trim leading/trailing hyphens
- Keep concise and clear

### 5. Compose the Branch Name

Use one of these formats:

- With issue number: `<prefix>/<issue-number>-<description-slug>`
- Without issue number: `<prefix>/<description-slug>`

Examples:

```bash
feat/123-add-user-authentication
fix/456-resolve-login-timeout
chore/update-readme-links
```

### 6. Create and Verify

```bash
git checkout -b <branch-name>
git branch --show-current
```

If the branch already exists, check it out instead:

```bash
git checkout <branch-name>
```

## Notes

- Prefer issue number in the branch name when available for traceability.
- Keep branch descriptions short (typically 3-8 words) while preserving intent.
- This skill is intended to be used directly, or as a sub-step inside `addressing-github-issues`.
