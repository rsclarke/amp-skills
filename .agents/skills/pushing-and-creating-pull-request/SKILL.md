---
name: pushing-and-creating-pull-request
description: Pushes the current branch and creates a GitHub pull request with a structured description. Use when asked to push, create a PR, or open a pull request.
---

# Pushing and Creating a Pull Request

Pushes the current branch to the remote and creates a pull request with a well-structured description synthesized from commit history.

## Prerequisites

- At least one commit on the current branch ahead of the base branch

## Workflow

### 1. Preflight

If a calling skill already verified state in this run, skip.

Otherwise:

```bash
git status --short --branch
```

- **None** — proceed (clean)
- **Only `??`** — warn the user; they will not be pushed. Proceed only with approval
- **Any `M A D R C`** — stop; commit (via `creating-conventional-commits`) or stash before pushing
- **Any `U` / `AA` / `DD`** — stop; resolve before pushing

Also confirm the current branch is **not** the default branch before pushing.

### 2. Gather Commit History

Collect all commits on the branch that are ahead of the base branch:

```bash
git log origin/main..HEAD --reverse --format="%h %s%n%n%b"
```

Parse each commit for:
- **Type** (`feat`, `fix`, `docs`, etc.) from the conventional commit prefix
- **Description** from the subject line
- **Body** for additional context
- **Issue references** (e.g., `Closes #N`, `Fixes #N`, `Refs #N`)

### 3. Fetch Referenced Issue Context

For each issue number referenced in commits or the branch name:

```bash
gh issue view ISSUE_NUMBER --json title,body,labels
```

Use the issue title and body to understand the motivation and reason for the change.

### 4. Determine PR Title

- **Single commit**: Use the commit subject line as the PR title.
- **Multiple commits**: Synthesize a title that captures the overall change, using conventional commit format (e.g., `feat: add findings service with pagination`).

The title should match the primary conventional commit type of the work.

### 5. Compose PR Description

Structure:

```markdown
A 2–3 sentence paragraph explaining what changed and why, drawing motivation from the referenced issue.

- One bullet per logical change (single commit → derive from body/diff; multiple commits → one bullet per commit, prefix stripped)
- Group related commits

Closes #N
```

Rules:
- Never insert newlines inside a paragraph or bullet — each is one unwrapped line. GitHub renders mid-paragraph newlines as literal breaks.
- Use `Closes`/`Fixes` for fully resolved issues, `Refs` otherwise. Place at end of body, collected from all commits and the branch name.

### 6. Push and Create the PR

```bash
git push -u origin HEAD
gh pr create --title "<title>" --body "<body>"
```

Escape special characters in `--body` for the shell.

## Conventional Commit Type to PR Title Mapping

- All `feat` → `feat:`
- All `fix` → `fix:`
- All `docs` → `docs:`
- All `chore` → `chore:`
- Mixed types → prefix of the primary type
