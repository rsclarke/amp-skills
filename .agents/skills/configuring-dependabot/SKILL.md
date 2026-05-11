---
name: configuring-dependabot
description: Creates or updates a .github/dependabot.yml file for a repository. Discovers package ecosystems in use, then writes a configuration with sensible schedules, grouping, and PR limits per the Dependabot options reference. Use when asked to add, enable, or update Dependabot configuration.
---

# Configuring Dependabot

Produces a `.github/dependabot.yml` that enables version updates for every package ecosystem the repository actually uses, with grouping and scheduling chosen to minimise PR noise.

Authoritative reference: <https://docs.github.com/en/code-security/reference/supply-chain-security/dependabot-options-reference>.

## Workflow

### 1. Check for an Existing Configuration

```bash
ls .github/dependabot.yml 2>/dev/null
```

If `.github/dependabot.yml` exists, read it — treat the task as an *update*, preserving deliberate user customisations (top-level `registries:` and per-update `registries: [...]` references, `target-branch`, `ignore`/`allow`, `reviewers`/`assignees`/`labels`, custom `commit-message`).

### 2. Discover Package Ecosystems

Scan the repository (one `rg --files` is enough) and map manifest files to their `package-ecosystem` value. Only enable an ecosystem if its manifest is actually present.

- `bun` — `bun.lock`
- `npm` — `package.json` with `package-lock.json`, `npm-shrinkwrap.json`, `pnpm-lock.yaml`, or `yarn.lock` (or `package.json` alone if the repo is not Bun-based)
- `bundler` — `Gemfile`
- `cargo` — `Cargo.toml`
- `composer` — `composer.json`
- `mix` — `mix.exs`
- `maven` — `pom.xml`
- `gradle` — `build.gradle`, `build.gradle.kts`, `settings.gradle*`
- `nuget` — `*.csproj`, `*.fsproj`, `*.vbproj`, `packages.config`
- `dotnet-sdk` — `global.json`
- `gomod` — `go.mod`
- `pip` — `requirements*.txt`, `setup.py`, `setup.cfg`, `Pipfile`, `pyproject.toml` (poetry), `*.in` (pip-compile)
- `uv` — `pyproject.toml` with `[tool.uv]` or `uv.lock`
- `pub` — `pubspec.yaml`
- `swift` — `Package.swift`
- `elm` — `elm.json`
- `julia` — `Project.toml`
- `conda` — `environment.yml`, `meta.yaml`
- `docker` — `Dockerfile*`, plus Kubernetes manifests or Helm `values.yaml` files that reference image tags
- `docker-compose` — `docker-compose*.y[a]ml`, `compose*.y[a]ml`
- `helm` — `Chart.yaml`
- `terraform` — `*.tf`, `*.tf.json`
- `opentofu` — `*.tofu` or Terraform tree managed by OpenTofu
- `nix` — `flake.lock`
- `devcontainers` — `.devcontainer/devcontainer.json` or `.devcontainer.json`
- `github-actions` — `.github/workflows/*.y[a]ml`, root `action.y[a]ml`
- `gitsubmodule` — `.gitmodules`
- `pre-commit` — `.pre-commit-config.yaml`
- `bazel` — `MODULE.bazel`, `WORKSPACE*`
- `vcpkg` — `vcpkg.json`
- `rust-toolchain` — `rust-toolchain*`

Detection notes:
- Choose **one** JS ecosystem per project from its lockfile; never emit both `bun` and `npm` for the same package set.
- `nix` requires a `flake.lock` — Dependabot updates the lockfile, not pinned refs in `flake.nix`.
- `github-actions` only updates external `uses: owner/repo@ref` references in `.github/workflows/*` and root `action.y[a]ml`. Local `./...` actions and `docker://` references are not updated.
- `helm` already updates Docker images referenced inside the chart — do not add a separate `docker` entry for the same chart directory unless non-chart manifests live there too.

For each ecosystem, also note the *locations* of its manifests so you can pick `directory: "/"` (single root) or `directories: [...]` (multiple sub-projects).

### 3. Decide Per-Ecosystem Strategy

For every ecosystem found, decide the four key settings before writing YAML.

**`directory` vs `directories`:**
- Single manifest at the root (or a build tool that aggregates from root, e.g. Maven reactor, Gradle multi-project, Cargo workspace, Go module): use `directory: "/"`.
- Independent sub-projects each with their own manifest (e.g. several Dockerfiles, npm workspaces with no root lockfile, multiple Terraform stacks): use `directories: [...]`.
- Prefer a single `updates` entry with `directories: [...]` over multiple entries for the same ecosystem/target-branch when the policy is identical — this enables `group-by: dependency-name` and avoids overlap errors.
- For `github-actions`, always use `directory: "/"` (Dependabot scans `.github/workflows/` automatically).

**`schedule.interval`:** default to `weekly`. Use `daily` only if the user asks. Use `monthly` only when the repo is clearly low-touch or the user explicitly wants lower churn (avoid for security-sensitive ecosystems).

**`open-pull-requests-limit`:** default `5` (matches GitHub's own default and keeps noise down). Raise to `10` only when the user wants faster update throughput; omit the key entirely if you have no reason to deviate.

**`cooldown`:** always set a small cooldown so freshly-released versions get a chance to settle before Dependabot opens a PR. Sensible defaults:

```yaml
cooldown:
  default-days: 3
  semver-major-days: 7
```

Increase `semver-major-days` (e.g. `14`) for risk-averse projects. `cooldown` applies only to version updates, not security updates, so it never delays vulnerability fixes.

**`groups`:** the goal is to bundle dependencies that move together (same vendor, same framework, related plugins) into one PR so reviewers see a coherent change. Derive groups from the actual manifests — read the dependency list and identify clusters by shared name prefix, organisation, or known framework family.

Dependabot assigns each dependency to the **first** matching group, so order groups narrowest → broadest:
1. Framework/family groups first, one per cluster you identified.
2. A catch-all `minor-and-patch` group with `update-types: ["minor", "patch"]` last, as the low-risk fallback.

`reference/family-groups.md` shows illustrative patterns for common ecosystems (Spring, React, AWS SDK, etc.) — use it for shape and naming inspiration, not as an exhaustive list. Most repos will have project-specific clusters not covered there; invent group names and patterns to fit what is in the manifests.

Leave `major` updates ungrouped so breaking changes are reviewed individually. When the same dependency lives in multiple `directories`, set `group-by: dependency-name` on the relevant group so a single PR updates it everywhere.

### 4. Compose `dependabot.yml`

Write to `.github/dependabot.yml`. Required top-level keys: `version: 2` and `updates:`.

Order each `updates` entry consistently for readability. Place specific family groups first and the `minor-and-patch` fallback last:

```yaml
- package-ecosystem: "<ecosystem>"
  directory: "/"            # or directories: [...]
  schedule:
    interval: "weekly"
  open-pull-requests-limit: 5
  cooldown:
    default-days: 3
    semver-major-days: 7
  groups:
    # specific families first (narrowest → broadest)
    spring-boot:
      patterns: ["org.springframework*"]
    # fallback last
    minor-and-patch:
      update-types: ["minor", "patch"]
```

Only add optional keys (`labels`, `assignees`, `reviewers`, `commit-message`, `versioning-strategy`, `target-branch`, `ignore`, `allow`, `registries`, `milestone`, `rebase-strategy`, `vendor`, `exclude-paths`, `pull-request-branch-name`, `insecure-external-code-execution`, `multi-ecosystem-groups`) when the user asks or the existing file already uses them — do not invent policy. Skip `enable-beta-ecosystems` (currently has no effect per the GitHub docs).

When updating an existing file, preserve order and comments where possible; only change what the task requires.

### 5. Validate

Confirm:
- `version: 2` is present.
- Every `updates` entry has `package-ecosystem`, `directory`/`directories`, and `schedule.interval`.
- Each ecosystem listed actually has a matching manifest in the repo.
- No two entries for the same ecosystem and target-branch overlap on directories.
- `package-ecosystem` values are spelled exactly as in the reference list above (e.g. `gomod`, not `go`; `gitsubmodule`, not `git-submodule`).
- Family `groups` precede the `minor-and-patch` fallback in each entry (first match wins).

## Done when

- `.github/dependabot.yml` exists and parses as YAML.
- Every detected ecosystem the user wants managed is configured with directory, schedule, PR limit, and grouping.
- Custom user settings from any pre-existing file are preserved.
