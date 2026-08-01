# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.1.4] - 2026-08-01

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
