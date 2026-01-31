---
name: creating-github-sub-issues
description: Creates GitHub issues with parent-child relationships using the gh CLI and REST API. Use when asked to create issues with sub-issues, parent issues, or issue hierarchies.
---

# Creating GitHub Issues with Sub-Issues

Creates GitHub issues with proper parent-child relationships visible in the GitHub UI sidebar.

## Prerequisites

- `gh` CLI installed and authenticated (`gh auth status`)
- Repository write access

## Workflow

### 1. Create the Parent Issue

```bash
gh issue create \
  --title "Parent Issue Title" \
  --body "Description of the parent issue..."
```

Note the issue URL returned (e.g., `https://github.com/owner/repo/issues/27`).

### 2. Create Sub-Issues

Create each sub-issue with a reference to the parent in the body:

```bash
gh issue create \
  --title "Sub-issue title" \
  --body "Description...

Parent issue: #27"
```

### 3. Link Sub-Issues to Parent

Get the issue ID (not issue number) for each sub-issue:

```bash
gh api repos/OWNER/REPO/issues/ISSUE_NUMBER --jq '.id'
```

Add sub-issues to the parent using the REST API with JSON input:

```bash
issue_id=$(gh api repos/OWNER/REPO/issues/28 --jq '.id')
gh api repos/OWNER/REPO/issues/27/sub_issues -X POST --input - <<< "{\"sub_issue_id\": $issue_id}"
```

The `sub_issue_id` must be passed as an integer, not a string.

### 4. Batch Add Multiple Sub-Issues

```bash
for issue_num in 28 29 30 31; do
  issue_id=$(gh api repos/OWNER/REPO/issues/$issue_num --jq '.id')
  gh api repos/OWNER/REPO/issues/27/sub_issues -X POST --input - <<< "{\"sub_issue_id\": $issue_id}"
done
```

## API Reference

### Add Sub-Issue

```
POST /repos/{owner}/{repo}/issues/{issue_number}/sub_issues
```

Request body:
```json
{
  "sub_issue_id": 123456789,
  "replace_parent": false
}
```

- `sub_issue_id` (required): The numeric ID of the issue to add as sub-issue
- `replace_parent` (optional): If true, replaces the sub-issue's current parent

### List Sub-Issues

```bash
gh api repos/OWNER/REPO/issues/27/sub_issues
```

### Remove Sub-Issue

```
DELETE /repos/{owner}/{repo}/issues/{issue_number}/sub_issues/{sub_issue_id}
```

### Get Parent Issue

```bash
gh api repos/OWNER/REPO/issues/28/parent
```

## Important Notes

- Sub-issues must belong to the same repository owner as the parent
- Up to 100 sub-issues per parent issue
- Up to 8 levels of nested sub-issues
- The deprecated `[tasklist]` markdown syntax does not create proper parent-child relationships
- Issue IDs are different from issue numbers; use the API to get the ID
