<div align="center">

# gh-workflows

**Centralized reusable GitHub Actions workflows for all amcheste repos. Change CI once, apply it everywhere.**

[![Validate](https://github.com/amcheste/gh-workflows/actions/workflows/validate.yml/badge.svg)](https://github.com/amcheste/gh-workflows/actions/workflows/validate.yml)
[![Version](https://img.shields.io/github/v/tag/amcheste/gh-workflows?label=version&sort=semver&color=0B0B0C)](https://github.com/amcheste/gh-workflows/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-1F4D3A.svg)](LICENSE)
[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/amcheste/gh-workflows/badge)](https://scorecard.dev/viewer/?uri=github.com/amcheste/gh-workflows)

</div>

---

Every amcheste repo used to carry its own copy of the standard CI workflows, stamped from [`repo-template`](https://github.com/amcheste/repo-template) at creation time. Copies drift: when a bug was found in the monthly release workflow, fixing it took 13 identical PRs. This repo replaces copy-at-creation with reference-forever. The logic lives here once; each repo keeps a thin caller stub that pins a release of this repo.

The model is documented in the [engineering handbook's centralized CI design note](https://github.com/amcheste/engineering-handbook/blob/develop/docs/design/centralized-ci-workflows.md).

## Available reusable workflows

| Workflow | Purpose | Caller needs |
|---|---|---|
| [`reusable-monthly-dependency-release.yml`](.github/workflows/reusable-monthly-dependency-release.yml) | On the caller's schedule, open a patch release PR if Dependabot commits landed on the base branch since the last release tag | `VERSION` file; "Allow GitHub Actions to create PRs" repo setting; `contents: write` and `pull-requests: write` |
| [`reusable-gitleaks.yml`](.github/workflows/reusable-gitleaks.yml) | Secret-scan CI backstop (the first line of defense stays the local pre-commit hook) | nothing special |
| [`reusable-validate.yml`](.github/workflows/reusable-validate.yml) | Docs/tooling validate: markdownlint, offline link check, optional shellcheck (`shellcheck-paths` input), commit lint, semver suggestion | `.markdownlint-cli2.jsonc`; required checks become `validate / Lint` and `validate / Commit Lint` |
| [`reusable-sast.yml`](.github/workflows/reusable-sast.yml) | Semgrep static analysis (`semgrep-config` input; `p/secrets` floor) | nothing special |
| [`reusable-scorecard.yml`](.github/workflows/reusable-scorecard.yml) | OpenSSF Scorecard with a default-branch guard (skips pushes to non-default branches instead of failing) | public repo for real scores |
| [`reusable-stale.yml`](.github/workflows/reusable-stale.yml) | Issue/PR hygiene: stale at 30 days, close at 37; `pinned`/`security` exempt | nothing special |
| [`reusable-release-drafter.yml`](.github/workflows/reusable-release-drafter.yml) | Accumulates draft release notes as PRs merge | `.github/release-drafter.yml` config |

Each reusable workflow's header comment contains the exact caller stub to copy into a consuming repo.

## How consuming repos use this

A stub in the consuming repo owns the triggers and pins a release tag:

```yaml
# .github/workflows/gitleaks.yml in any repo
name: Gitleaks
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]
permissions:
  contents: read
jobs:
  gitleaks:
    uses: amcheste/gh-workflows/.github/workflows/reusable-gitleaks.yml@v1.0.0
```

- **Pinning:** callers pin exact release tags (`@v1.0.0`), never branches or moving tags. Release tags here are protected by a ruleset (no creation, deletion, or moves outside the release flow), so a pin is immutable.
- **Propagation:** Dependabot treats reusable-workflow references like actions and opens bump PRs in every caller when a new release is tagged here. Fleet propagation is governed by each repo's normal PR review, at Dependabot cadence.
- **Versioning contract:** patch and minor releases are behavior-compatible for callers; anything that changes required inputs, permissions, or observable behavior in a breaking way is a major bump.

## How this repo works

- Standard branch model: PRs target `develop`; releases promote `develop` to `main` via CLI `--no-ff` merge and a `v*` tag.
- This repo consumes its own reusables by local path (`uses: ./.github/workflows/reusable-*.yml`), so every PR here exercises the code it changes.
- Lint runs [actionlint](https://github.com/rhysd/actionlint) over all workflow YAML plus markdownlint over the docs.

## License

[MIT](LICENSE)
