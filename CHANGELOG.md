# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `reusable-monthly-dependency-release.yml`: the monthly dependency release as a `workflow_call` reusable, with the tag-lookup fix baked in (`git tag --list` instead of `git describe`, `fetch-tags: true`) and a `base-branch` input.
- `reusable-gitleaks.yml`: the secret-scan CI backstop as a `workflow_call` reusable.
- Local caller stubs so this repo consumes its own reusables on every PR.
- Real lint surface: actionlint over all workflow YAML plus markdownlint.
- `LICENSE`, `VERSION`, `CHANGELOG.md` (the template does not ship them yet).
