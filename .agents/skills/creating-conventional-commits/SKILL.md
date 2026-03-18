---
name: creating-conventional-commits
description: Creates Git commits that follow the Conventional Commits 1.0.0 specification as work progresses. Use when asked to commit changes during implementation or before opening a pull request.
---

# Creating Conventional Commits

Creates Git commits that follow the [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) specification.

## Prerequisites

- Git repository with write access
- A clear logical unit of work ready to commit
- Staged changes, or intent to stage specific files

## Workflow

### 1. Verify Repository State

```bash
git status --short
git branch --show-current
```

Confirm you are on the intended branch and understand which files are modified before staging.

### 2. Stage a Logical Change Set

Stage only files that belong to one coherent change:

```bash
git add <files>
git diff --staged
```

If the work contains unrelated edits, split them into separate commits.

### 3. Choose the Commit Type

Use these conventional types based on intent:

| Change Intent | Type |
|---------------|------|
| New user-facing behavior | `feat` |
| Bug fix or regression fix | `fix` |
| Documentation-only update | `docs` |
| Formatting-only (no logic change) | `style` |
| Internal restructuring without behavior change | `refactor` |
| Test additions or test-only updates | `test` |
| Tooling, maintenance, or dependency work | `chore` |

### 4. Compose the Commit Message

Follow this structure:

```text
<type>[optional scope][!]: <short imperative summary>

[optional body]

[optional footer(s)]
```

Guidelines:
- Keep the summary concise and specific (typically under 72 characters).
- Use an optional scope when it improves clarity, for example `fix(auth): handle token refresh`.
- Use `!` for breaking changes and explain the break in the body or footer.
- Add issue references in footers when relevant: `Closes #123`, `Fixes #123`, or `Refs #123`.

### 5. Commit and Verify

```bash
git commit -m "<type>: <summary>"
git log -1 --format="%h %s"
```

For body and footers, prefer multiple `-m` flags:

```bash
git commit -m "fix(auth): handle expired refresh token" \
  -m "Rejects malformed refresh payloads before token verification." \
  -m "Fixes #123"
```

### 6. Continue During Ongoing Work

Repeat this process as implementation progresses, committing each logical step separately instead of batching all changes into one large commit.

## Notes

- Make commits incrementally throughout implementation to preserve reviewable history.
- Do not mix unrelated concerns in the same commit.
- This skill is intended to be used directly, or as a sub-step inside `addressing-github-issues`.
