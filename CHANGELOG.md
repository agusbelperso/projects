# Changelog

---

## 2026-07-04 (ignore macOS Finder junk)

### Changed
- `.gitignore` — added a macOS section (`.DS_Store`, `.AppleDouble`, `.LSOverride`, `.Spotlight-V100`, `.Trashes`). The existing `.gitignore` was a Visual Studio template with no macOS entries, so Finder's auto-generated `.DS_Store` kept showing up as untracked.

### Context
`.DS_Store` isn't something anyone adds deliberately — Finder writes it just from browsing a folder. It was already excluded from commits per the staging rule in `push.md`, but without a `.gitignore` entry it kept resurfacing in `git status`.

## 2026-07-04 (repo bootstrap: CLAUDE.md, push skill, changelog)

### Added
- `CLAUDE.md` — guidance for Claude Code operating in this repo: repo purpose (personal skill/prompt library, no build/lint/test tooling), hard constraints and HITL triggers, and a rundown of how each skill file works and interconnects.
- `CHANGELOG.md` — this file, now a mandatory step in the push workflow.
- `push.md` — commit/push checklist for this repo (this-repo staging rules, CHANGELOG-first workflow, conventional commit format with scopes tied to this repo's skill files).

### Changed
- Replaced `finanalytics-push.md` with `push.md` — the original was written for a different account's `~/repos/FinAnalytics` repo (FinAnalytics-specific scopes like `mbr`, `mbr/channel`, `data`); this version targets this repo's actual remote (`agusbelperso/projects`), drops the FinAnalytics-only scopes in favor of generic ones tied to this repo's skill files, and keeps the mandatory CHANGELOG-before-commit rule.
- `prompt-builder.md` — added missing YAML frontmatter (`name`/`description`), bringing it in line with the other skill files so it's actually discoverable as a skill instead of loose instructions.
- `log-this.md` — fixed a labeling bug where the frontmatter and Step 2 header said "two questions" while three were listed; relabeled to "three questions" to match. Also replaced the Step 4 JSONL append, which built the line via `echo '<json>' >>` and would break on any apostrophe in a free-text field (`wins`, `risks`, etc.), with a `python3 -c` block that builds the entry as a dict and writes it via `json.dumps`.

### Removed
- Standalone `Claude MD Rules` file — its hard constraints and HITL triggers were folded directly into `CLAUDE.md` so there's a single source of truth instead of two files.

### Context
This repo started as a loose collection of personal Claude Code skill files with no root-level guidance file and one skill (`finanalytics-push.md`) that was actually written for a different repo on a different account. This entry establishes the baseline: a `CLAUDE.md` future sessions can rely on, and a push workflow that actually matches this repo instead of a borrowed one.
