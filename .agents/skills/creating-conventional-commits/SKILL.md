---
name: creating-conventional-commits
description: Creates Git commits that follow the Conventional Commits 1.0.0 specification as work progresses. Use when asked to commit changes during implementation or before opening a pull request.
---

# Creating Conventional Commits

Creates Git commits that follow the [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) specification.

## Prerequisites

- A clear logical unit of work ready to commit
- Known list of files to include in the commit

## Workflow

### 1. Verify Repository State

```bash
git status --short
git branch --show-current
```

Confirm you are on the intended branch and understand which files are modified before staging.

### 2. Identify the Logical Change Set

Determine which files belong to this commit. If the work contains unrelated edits, split them into separate commits.

Preview the changes for the intended files:

```bash
git diff HEAD -- <files>
```

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

Commit only the intended files by passing them directly to `git commit`. This avoids accidentally including unrelated changes that may already be in the index:

```bash
git commit <files> -m "<type>: <summary>"
git log -1 --format="%h %s"
```

For body and footers, prefer multiple `-m` flags:

```bash
git commit <files> -m "fix(auth): handle expired refresh token" \
  -m "Rejects malformed refresh payloads before token verification." \
  -m "Fixes #123"
```

### 6. Continue During Ongoing Work

Repeat this process as implementation progresses, committing each logical step separately instead of batching all changes into one large commit.

## Notes

- Make commits incrementally throughout implementation to preserve reviewable history.
- Do not mix unrelated concerns in the same commit.
- This skill is intended to be used directly, or as a sub-step inside `addressing-github-issues`.
