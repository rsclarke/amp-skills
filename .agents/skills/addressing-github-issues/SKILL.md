---
name: addressing-github-issues
description: Addresses GitHub issues end-to-end. Fetches issue context, creates a working branch, and implements the fix or feature. Use when given an issue number to work on.
---

# Addressing GitHub Issues

Takes a GitHub issue number and works through it end-to-end: fetches context, creates a working branch, and implements the solution.

## Workflow

### 1. Preflight

```bash
git status --short --branch
```

- **None** — proceed (clean)
- **Only `??`** — proceed; the commits step will handle them
- **Any `M A D R C`** — stop and ask the user (commit, stash, or discard)
- **Any `U` / `AA` / `DD`** — stop (merge conflict)

Sub-skills below will skip their own preflight because this run already verified state.

### 2. Fetch the Issue

```bash
gh issue view ISSUE_NUMBER --json title,body,labels,assignees,comments
```

Parse the response to understand:
- What the issue is requesting
- Any acceptance criteria
- Relevant labels (bug, feature, enhancement, etc.)

### 3. Create the Working Branch

Use the `creating-conventional-branches` skill with the issue details from Step 2 (number, title, and labels).

At minimum, ensure the branch includes:
- A conventional prefix (for example `feat/`, `fix/`, `chore/`)
- The issue number
- A concise description slug

### 4. Review Related Code

Before making changes, understand the codebase context:

1. **Search for related files** - Use finder to locate code mentioned in or related to the issue
2. **Read existing implementations** - Understand patterns, conventions, and dependencies
3. **Check for tests** - Find existing test files that may need updates
4. **Review AGENTS.md** - Follow any project-specific workflows or guidelines

### 5. Implement the Solution

Follow the project's established patterns:

1. Make the necessary code changes
2. Add or update tests as appropriate
3. Update documentation if needed
4. Run linting and type checking commands
5. Ensure all tests pass

### 6. Commit with Conventional Commits

Use the `creating-conventional-commits` skill to commit each logical change as work progresses.

When the issue is fully resolved, ensure commit footers include the appropriate reference (for example `Closes #ISSUE_NUMBER` or `Fixes #ISSUE_NUMBER`).

### 7. Summary

After completing the work, provide:
- Branch name created
- Summary of changes made
- Files modified
- Tests added/updated
- Any follow-up items or considerations
