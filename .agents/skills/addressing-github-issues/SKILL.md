---
name: addressing-github-issues
description: Addresses GitHub issues end-to-end. Creates a conventional branch, reviews related code, and implements the fix or feature. Use when given an issue number to work on.
---

# Addressing GitHub Issues

Takes a GitHub issue number and works through it end-to-end: creates a branch, understands the context, and implements the solution.

## Prerequisites

- `gh` CLI installed and authenticated (`gh auth status`)
- Repository write access
- Clean git working directory (or stash uncommitted changes)

## Workflow

### 1. Fetch the Issue

```bash
gh issue view ISSUE_NUMBER --json title,body,labels,assignees,comments
```

Parse the response to understand:
- What the issue is requesting
- Any acceptance criteria
- Relevant labels (bug, feature, enhancement, etc.)

### 2. Create a Conventional Branch

Branch naming follows the [Conventional Branch](https://conventional-branch.github.io/) specification:

**Format**: `<prefix>/<issue-number>-<description>`

**Prefixes based on issue type**:
- `feature/` or `feat/` - New features (label: `enhancement`, `feature`)
- `fix/` or `bugfix/` - Bug fixes (label: `bug`)
- `hotfix/` - Urgent production fixes (label: `critical`, `urgent`)
- `chore/` - Non-code tasks like docs, dependencies (label: `documentation`, `chore`, `dependencies`)

**Rules**:
- Use lowercase letters, numbers, and hyphens only
- No consecutive, leading, or trailing hyphens
- Keep descriptions concise but clear

**Examples**:
```bash
git checkout -b feat/123-add-user-authentication
git checkout -b fix/456-resolve-login-timeout
git checkout -b chore/789-update-readme
```

Determine the prefix from the issue labels. Default to `feat/` if unclear.

```bash
git checkout -b <prefix>/<issue-number>-<short-description>
```

### 3. Review Related Code

Before making changes, understand the codebase context:

1. **Search for related files** - Use finder to locate code mentioned in or related to the issue
2. **Read existing implementations** - Understand patterns, conventions, and dependencies
3. **Check for tests** - Find existing test files that may need updates
4. **Review AGENTS.md** - Follow any project-specific workflows or guidelines

### 4. Implement the Solution

Follow the project's established patterns:

1. Make the necessary code changes
2. Add or update tests as appropriate
3. Update documentation if needed
4. Run linting and type checking commands
5. Ensure all tests pass

### 5. Commit with Conventional Commits

Use [Conventional Commits](https://www.conventionalcommits.org/) format:

```bash
git add .
git commit -m "<type>: <description>

Closes #ISSUE_NUMBER"
```

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### 6. Summary

After completing the work, provide:
- Branch name created
- Summary of changes made
- Files modified
- Tests added/updated
- Any follow-up items or considerations

## Label to Prefix Mapping

| Labels | Prefix |
|--------|--------|
| `enhancement`, `feature`, `feat` | `feat/` |
| `bug`, `defect` | `fix/` |
| `critical`, `urgent`, `hotfix` | `hotfix/` |
| `documentation`, `docs` | `chore/` |
| `dependencies`, `deps` | `chore/` |
| `chore`, `maintenance` | `chore/` |
| `release` | `release/` |
| (no matching labels) | `feat/` |

## Notes

- Always check `gh auth status` before starting
- Verify you're on the correct repository with `gh repo view`
- If the working directory is dirty, prompt the user before proceeding
