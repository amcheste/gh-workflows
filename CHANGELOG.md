# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Fixed

- `reusable-stale.yml`'s caller-stub example (in this file's own header comment) was missing `issues: write` / `pull-requests: write` and pinned a stale `@v1.1.0`. Every repo that copied it got `startup_failure` on every scheduled Stale run since v1.1.0's initial release.

### Docs

- Backfilled changelog entries for `v1.1.1`-`v1.1.3` (below), which shipped without one.

## [1.1.3] - 2026-07-09

### Fixed

- `reusable-scorecard.yml`: dropped `permissions: read-all` from the documented caller-stub example. `read-all` grants nothing for `id-token` (it has no read level, only none/write), so a caller following the example got `startup_failure` on every Scorecard run — `read-all` looked generous but was actually the same bug as the missing-permission case it was meant to guard against.

> **Retroactive note (2026-07-20):** this should have been a major bump. It changes what a caller must grant to avoid `startup_failure`, which is a breaking change under this project's own versioning contract (see [`centralized-ci-workflows.md`](https://github.com/amcheste/engineering-handbook/blob/develop/docs/design/centralized-ci-workflows.md)). It shipped as a patch because the commit used `fix:` instead of `fix!:`, so the semver-suggestion tooling (and the human cutting the release) never saw a major-bump signal. Combined with v1.1.1 and v1.1.2 below, every caller that took these "routine" Dependabot patch bumps silently lost CI across gitleaks, SAST, and scorecard — not discovered until a manual fleet audit on 2026-07-20.

## [1.1.2] - 2026-07-09

### Fixed

- `reusable-sast.yml` and `reusable-scorecard.yml`: their jobs request `security-events: write` (and `reusable-scorecard.yml` also `id-token: write`), which the documented caller-stub examples didn't grant — same class of bug as v1.1.1's gitleaks fix, this time on the SAST and Scorecard callers.

> **Retroactive note (2026-07-20):** same breaking-change classification issue as v1.1.3 above — shipped as `fix:`/patch, should have been `fix!:`/major.

## [1.1.1] - 2026-07-08

### Fixed

- `reusable-gitleaks.yml`: the documented caller-stub example was missing `pull-requests: read`, which `gitleaks-action` needs to list a PR's commits and compute its scan range. Without it, a caller following the example 403s on every pull-request-triggered run.

> **Retroactive note (2026-07-20):** same breaking-change classification issue as v1.1.3 above — shipped as `fix:`/patch, should have been `fix!:`/major. This is the first instance of the pattern; see the [engineering-handbook incident writeup](https://github.com/amcheste/engineering-handbook/blob/develop/docs/design/centralized-ci-workflows.md) for the full timeline across all three releases and its fleet-wide impact.

## [1.1.0] - 2026-07-08

### Added

- Five more reusable workflows, completing the universal tier plus the docs/tooling validate: `reusable-validate.yml` (markdownlint, offline link check, optional shellcheck via `shellcheck-paths`, commit lint, semver suggestion), `reusable-sast.yml` (`semgrep-config` input, `p/secrets` floor), `reusable-scorecard.yml` (with a default-branch guard so pushes to non-default branches skip instead of failing), `reusable-stale.yml`, and `reusable-release-drafter.yml`.
- This repo now self-consumes scorecard, stale, release-drafter, and sast as local-path stubs (validate stays local: this repo's lint is actionlint).

## [1.0.1] - 2026-07-08

### Fixed

- Existing-release-PR guard now matches PR titles only. gh's `--search` also matches PR bodies, so any open PR merely mentioning "chore: release" suppressed the monthly release (found by the fleet pilot).

### Added

- Optional `RELEASE_PR_TOKEN` secret on the monthly release reusable. PRs created with the default `GITHUB_TOKEN` do not trigger `pull_request` workflows, so required checks never report on them; passing an App installation token or PAT gets a release PR whose CI runs on its own. Without it, the release PR body now instructs a one-time close/reopen.

## [1.0.0] - 2026-07-08

### Added

- `reusable-monthly-dependency-release.yml`: the monthly dependency release as a `workflow_call` reusable, with the tag-lookup fix baked in (`git tag --list` instead of `git describe`, `fetch-tags: true`) and a `base-branch` input.
- `reusable-gitleaks.yml`: the secret-scan CI backstop as a `workflow_call` reusable.
- Local caller stubs so this repo consumes its own reusables on every PR.
- Real lint surface: actionlint over all workflow YAML plus markdownlint.
- `LICENSE`, `VERSION`, `CHANGELOG.md` (the template does not ship them yet).
