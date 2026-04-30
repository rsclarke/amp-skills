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

Extract the request, acceptance criteria, and labels.

### 3. Create the Working Branch

Use the `creating-conventional-branches` skill with the issue number, title, and labels.

### 4. Implement the Solution

Implement the change following project conventions and AGENTS.md.

### 5. Commit with Conventional Commits

Use the `creating-conventional-commits` skill. Include `Closes #ISSUE_NUMBER` (or `Fixes #ISSUE_NUMBER`) in the footer of the commit that resolves the issue.
