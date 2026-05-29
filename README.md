# Amp Skills

A collection of custom skills for [Amp](https://ampcode.com).

## Available Skills

| Skill | Description |
|-------|-------------|
| [addressing-github-issues](.agents/skills/addressing-github-issues/SKILL.md) | Implements a GitHub issue up to the point of human review. Fetches issue context, creates a working branch, implements the change, and commits — stopping before push/PR |
| [configuring-dependabot](.agents/skills/configuring-dependabot/SKILL.md) | Creates or updates `.github/dependabot.yml` by detecting package ecosystems in the repo and applying sensible schedules, grouping, and PR limits |
| [creating-conventional-branches](.agents/skills/creating-conventional-branches/SKILL.md) | Creates and checks out Git branches that follow Conventional Branch naming |
| [creating-conventional-commits](.agents/skills/creating-conventional-commits/SKILL.md) | Creates Git commits that follow the Conventional Commits 1.0.0 specification as work progresses |
| [creating-github-sub-issues](.agents/skills/creating-github-sub-issues/SKILL.md) | Creates GitHub issues with parent-child relationships using the gh CLI and REST API |
| [hardening-github-actions](.agents/skills/hardening-github-actions/SKILL.md) | Audits and hardens GitHub Actions workflows, composite actions, and Dependabot configuration with zizmor, then adds an ongoing zizmor-action workflow |
| [pushing-and-creating-pull-request](.agents/skills/pushing-and-creating-pull-request/SKILL.md) | Pushes the current branch and creates a GitHub pull request with a structured description |

## Installation

See the [Amp manual](https://ampcode.com/manual#agent-skills).
