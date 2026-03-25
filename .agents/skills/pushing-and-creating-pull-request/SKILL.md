---
name: pushing-and-creating-pull-request
description: Pushes the current branch and creates a GitHub pull request with a structured description. Use when asked to push, create a PR, or open a pull request.
---

# Pushing and Creating a Pull Request

Pushes the current branch to the remote and creates a pull request with a well-structured description synthesized from commit history.

## Prerequisites

- At least one commit on the current branch ahead of the base branch

## Workflow

### 1. Verify State

```bash
git status
git branch --show-current
```

Confirm:
- Working directory is clean (no uncommitted changes)
- On the correct feature branch (not `main`)

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

Structure the body as follows:

```markdown
A concise paragraph explaining what this PR does and why. Derived from
the issue context (motivation/reason for change) and commit messages.

- First change derived from commit messages
- Second change derived from commit messages
- Additional changes as needed

Closes #N
```

**Rules for composing each part:**

#### Summary paragraph
- Explain **what** changed and **why** (the motivation)
- If an issue is referenced, incorporate its context for the "why"
- Keep to 2–3 sentences

#### Changes list
- One bullet per logical change
- For a **single commit**: derive bullets from the commit body, or summarize the diff if the body is sparse
- For **multiple commits**: one bullet per commit, using the commit subject (strip the conventional commit prefix for readability)
- Group related commits if they address the same concern

#### Issue References
- Place `Closes #N` or `Fixes #N` at the end of the body (not in the summary paragraph)
- Use `Closes` for issues fully resolved by the PR
- Use `Refs #N` for issues that are related but not fully resolved
- Collect references from all commits and the branch name

### 6. Push and Create the PR

```bash
git push -u origin HEAD
gh pr create --title "<title>" --body "<body>"
```

### 7. Summary

After creating the PR, provide:
- PR URL (from `gh pr create` output)
- PR title
- Issues referenced
- Number of commits included

## Conventional Commit Type to PR Title Mapping

| Commit Types in Branch | PR Title Prefix |
|------------------------|-----------------|
| All `feat` | `feat:` |
| All `fix` | `fix:` |
| All `docs` | `docs:` |
| All `chore` | `chore:` |
| Mixed types | Use the prefix of the primary/most significant type |

## Notes

- If the working directory has uncommitted changes, prompt the user before proceeding
- When there are multiple commits, read all of them before composing the description — do not base the PR solely on the first or last commit
- Escape special characters in the `--body` argument to avoid shell interpretation issues
