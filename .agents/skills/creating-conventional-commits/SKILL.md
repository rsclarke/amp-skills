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

### 1. Preflight

If a calling skill already verified state in this run, skip the `git status --short --branch` invocation but still apply the untracked-file rule in §2.

```bash
git status --short --branch
```

- **None** — nothing to commit; stop
- **Any `M A D R C`** — proceed to §2
- **Any `??`** — proceed to §2; intended untracked files are staged there
- **Any `U` / `AA` / `DD`** — stop; resolve before committing

### 2. Identify the Logical Change Set

Determine which files belong to this commit. If the work contains unrelated edits, split them into separate commits.

Preview the changes for the intended files:

```bash
git diff HEAD -- <files>
```

If any of the intended files are **untracked** (`??` in `git status`), stage **only those specific files** first so git recognises them — do not run a bare `git add` or `git add .`:

```bash
git add <intended untracked files only>
```

### 3. Choose the Commit Type

Use these conventional types based on intent:

- `feat` — new user-facing behavior
- `fix` — bug fix or regression fix
- `docs` — documentation-only update
- `style` — formatting-only (no logic change)
- `refactor` — internal restructuring without behavior change
- `test` — test additions or test-only updates
- `chore` — tooling, maintenance, or dependency work

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
