---
name: addressing-github-issues
description: Addresses GitHub issues end-to-end. Fetches issue context, creates a working branch, and implements the fix or feature. Use when given an issue number to work on.
---

# Addressing GitHub Issues

Takes a GitHub issue number and works through it end-to-end: fetches context, creates a working branch, and implements the solution.

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

### 2. Create the Working Branch

Use the `creating-conventional-branches` skill with the issue details from Step 1 (number, title, and labels).

At minimum, ensure the branch includes:
- A conventional prefix (for example `feat/`, `fix/`, `chore/`)
- The issue number
- A concise description slug

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

## Notes

- Always check `gh auth status` before starting
- Verify you're on the correct repository with `gh repo view`
- If the working directory is dirty, prompt the user before proceeding
