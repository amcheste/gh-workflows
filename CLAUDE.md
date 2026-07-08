# CLAUDE.md

This file is read by Claude Code at the start of every session in this repo.

---

## About This Repo

This repo is the central home for reusable GitHub Actions workflows shared by every amcheste repo. Workflow logic lives here once, under `.github/workflows/reusable-*.yml`; each consuming repo keeps only a thin caller stub that pins a release tag of this repo. Fix or improve CI here, cut a release, and Dependabot propagates the new pin to every caller.

- Reusable workflows are the product. Treat changes to them like library API changes: a breaking change to inputs or behavior is a major version bump.
- This repo consumes its own reusables by local path (`uses: ./.github/workflows/...`), so its own CI exercises the current branch.
- Lint here means actionlint over all workflow YAML plus markdownlint.
- The versioning contract and rollout model are documented in the engineering-handbook design note on centralized CI.

---

## Developer Preferences

### Editor

- Primary: Vim
- AI editor: Cursor

### Shell

- zsh, minimal prompt

### Git & GitHub Workflow

- **Branch model:** `main` = latest release. `develop` = integration branch.
- Always branch from `develop`, never commit directly
- PRs always target `develop`
- `main` is only updated via CLI merge (`git merge --no-ff origin/develop`) by `/publish-release` — **never via a GitHub PR**. GitHub's merge button squash-merges by default, dropping ancestry and causing conflicts on the next release.
- Conventional commits: `feat:`, `fix:`, `docs:`, `chore:`

### Scripting Standards

- Shell scripts must pass `shellcheck`
- Use `set -euo pipefail`
- Scripts should be idempotent

---

## Brand

This repo descends from [`amcheste/repo-template`](https://github.com/amcheste/repo-template), which is brand-aligned with [`@amcheste/brand`](https://github.com/amcheste/alanchester-brand). Badge colors (Hunter Green `#1F4D3A`, Ink `#0B0B0C`) match the brand by default.

When generating prose, follow the brand voice rules at [`voice.md`](https://github.com/amcheste/alanchester-brand/blob/main/docs/voice.md): no em dashes in prose, calibrated hedges over weak ones, lowercase eyebrows, numerical specificity. Hunter green is reserved for data, pivots, and the δ; don't use it as decoration.

For deeper brand integration (palette adoption, mark embedding, full theming sweep), paste [`docs/theming-prompt.md`](https://github.com/amcheste/alanchester-brand/blob/main/docs/theming-prompt.md) from the brand repo into a Claude Code session in this repo.

---

## Learned Preferences

<!-- Claude Code will suggest additions here as patterns emerge across sessions -->
