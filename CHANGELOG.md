# Changelog

---

## 2026-07-05 (adopt financial-analyst; fill in HR skills' missing references)

### Added
- `.claude/skills/financial-analyst/` — ported whole from the `claude-skills` remote's `finance` bundle (`SKILL.md`, `references/{financial-ratios-guide,valuation-methodology,forecasting-best-practices}.md`, `assets/{dcf_analysis_template,forecast_report_template,variance_report_template}.md` + sample/expected-output JSON, `scripts/{ratio_calculator,budget_variance_analyzer,dcf_valuation,forecast_builder}.py`), invocable as `/financial-analyst`. 1 of 3 skills in that bundle; `business-investment-advisor` and `saas-metrics-coach` were not adopted.

### Changed
- `docs/Strategic HR Cost Management.md` → `.claude/skills/operations-manager/references/cost_optimization.md` — renamed to match the file its own SKILL.md's "Reference Materials" section already expected but didn't have.
- `docs/hr-forecasting-slides-reference.md` → `.claude/skills/people-analytics/references/hr_metrics.md` — same fix, for people-analytics' expected-but-missing reference.
- `CLAUDE.md` — added `/financial-analyst` to the inventory, updated the `/operations-manager` and `/people-analytics` entries to reflect which of their expected reference files now actually exist vs. still don't, and removed the now-empty `docs/` section.

### Context
`financial-analyst` demonstrated what a skill's `references/` folder is supposed to look like (real, topic-specific docs) in contrast to the hr-operations skills, whose `references/` folders were entirely missing (confirmed missing upstream too, not just in this repo). The two `docs/` files were originally kept separate because their content didn't match either skill's expected reference filenames closely enough — but on reflection, `Strategic HR Cost Management.md` (a bottom-up cost model) is a direct fit for operations-manager's expected `cost_optimization.md`, and `hr-forecasting-slides-reference.md`'s KPI content (turnover, retention, hiring speed) matches people-analytics' expected `hr_metrics.md` closely enough to fill that gap instead of sitting outside the skill system. `docs/` is now empty and removed. Neither skill's other missing reference files (`process_design.md`, `lean_operations.md`, `vendor_management.md`, `predictive_models.md`, `survey_design.md`, `data_ethics.md`) have been reconstructed — still noted as gaps in `CLAUDE.md`.

## 2026-07-04 (add docs folder with HR forecasting slide reference)

### Added
- `docs/hr-forecasting-slides-reference.md` — text extraction of 4 slide screenshots from a SlideTeam "Cost Management Through Accurate HR Forecasting" PPT template (dashboard mockups, KPI slide, and a risk matrix), kept as a plain-text reference since the source page renders slides as images with no extractable text.
- `docs/Strategic HR Cost Management.md` — copy of the same-named file that already existed at the repo root, duplicated into `docs/` at the user's request.

### Context
This is the first use of a `docs/` folder in this repo, for saving reference material (like slide/image content the user wants preserved as text) that isn't a skill file itself. The root-level `Strategic HR Cost Management.md` was left in place and untracked; only the `docs/` copy is part of this commit.

## 2026-07-04 (adopt operations-manager and people-analytics skills)

### Added
- `.claude/skills/operations-manager/SKILL.md` + `scripts/{capacity_planner,process_mapper,sla_tracker}.py` — workforce planning, process optimization, compliance, and operational-efficiency skill, invocable as `/operations-manager`.
- `.claude/skills/people-analytics/SKILL.md` + `scripts/{attrition_predictor,headcount_planner,survey_analyzer}.py` — workforce analytics, attrition modeling, engagement analysis, and compensation-benchmarking skill, invocable as `/people-analytics`.

### Changed
- `CLAUDE.md` — added both skills to the skill inventory, including their overlap/relationship (people-analytics as the data foundation operations-manager draws on).

### Context
Both were adopted individually from the `claude-skills` reference remote's `hr-operations` bundle (`borghei/Claude-Skills`), which packages 4 skills via a `.claude-plugin` manifest — the plugin installs all 4 as a unit with no partial-install option, so a direct folder copy of just these 2 was more targeted than installing the plugin. `hr-business-partner` and `talent-acquisition` (the other 2 in the bundle) were intentionally not adopted. The upstream bundle's own `CLAUDE.md` was not copied verbatim — it documents all 4 skills, references sibling domains that don't exist in this repo, and its "planned Python tools" section names scripts that don't match what actually ships in the `operations-manager`/`people-analytics` folders (e.g. it references `org_chart_analyzer.py` and `compensation_benchmarker.py`, which don't exist). Only the accurate, applicable parts were folded into this repo's own `CLAUDE.md`.

## 2026-07-04 (move coach/context-window to global, strip sensitive references)

### Removed
- `.claude/skills/coach/` and `.claude/skills/context-window/` — removed from this repo entirely (both were still untracked/uncommitted). Relocated in cleaned-up form to `~/.claude/skills/coach/SKILL.md` and `~/.claude/skills/context-window/SKILL.md` so they work across every repo, not just this one.

### Changed
- `CLAUDE.md` — dropped `/coach` and `/context-window` from this repo's skill inventory (with a pointer to their new global location), and added the previously-undocumented `/data-analysis` skill to the inventory.

### Context
`coach.md` hardcoded a former/different account's home directory (`/Users/abeltraminopersoglia/...`), the user's name inline, and cross-referenced an external "10x-coach" framework and "CEO-defined metrics" tied to a former employer's internal system — none of which belonged in a personal, portable skill. The rewritten global version: uses `~/.claude/coaching_observations.md` and `~/.claude/CLAUDE.md` (no hardcoded username), refers to "the user" generically, drops the 10x-coach cross-reference and employer-specific jargon (Query Execution Gate, orchestrator Phase checks, `mbr-wbr-analyst`/`unit-economics` skill names) in favor of generic equivalents, so the five-dimension scoring applies to whatever the user is actually working on. `context-window.md` had no sensitive content, but its model-ID table was stale and was updated to current model IDs while moving it alongside `coach`.

## 2026-07-04 (adopt data-analysis skill from external remote)

### Added
- `.claude/skills/data-analysis/SKILL.md` — end-to-end data analysis assistant (Excel/CSV analysis, business metrics, ROI, HTML report generation, PPTX export) ported from the `data-skill` reference remote (`dongzhang84/data-analysis-skill`), invocable as `/data-analysis`.
- `.claude/skills/data-analysis/references/{report-styles,html-templates,workflows,domain-knowledge}.md` — supporting reference docs the SKILL.md points to for style parameters, HTML component patterns, the detailed multi-expert workflow spec, and domain-specific analysis knowledge.
- `.claude/skills/data-analysis/scripts/{read_excel.py,read_csv.py,html2pptx.js}` — executable helpers the skill shells out to for reading spreadsheets and converting HTML reports to PPTX. Each script self-installs its own missing dependencies (pandas/openpyxl/tabulate/chardet via pip; pptxgenjs/puppeteer via npm) on first run — nothing was installed as part of this commit.
- `.claude/skills/data-analysis/package.json` — documents the two npm packages `html2pptx.js` auto-installs (`pptxgenjs`, `puppeteer`).

### Context
The upstream repo already used the `.claude/skills/<name>/SKILL.md` layout this repo's own skills were just migrated to (see the entry below), so this was a direct folder copy rather than a reformat. The remote's own `README.md`, `LICENSE`, `sample-data/`, `sample-output/`, and `.gitignore` were left behind — those describe the standalone upstream repo, not the skill package itself. `.claude/skills/coach/` and `.claude/skills/context-window/` remain untracked/pending review from prior work and are untouched by this commit.

## 2026-07-04 (make skills reachable via / command)

### Changed
- `push.md` → `.claude/skills/push/SKILL.md`, `prompt-builder.md` → `.claude/skills/prompt-builder/SKILL.md`, `log-this.md` → `.claude/skills/log-this/SKILL.md` — moved into the `.claude/skills/<name>/SKILL.md` layout Claude Code requires for a skill to be invocable as `/<name>`. Root-level `.md` files were never actually reachable via slash command.
- `CLAUDE.md` — updated the skill inventory to reference `/prompt-builder`, `/coach`, `/context-window`, `/log-this`, `/push` and their `.claude/skills/` paths instead of root-level filenames; noted the `.claude/skills/<name>/SKILL.md` requirement for any new skill.

### Context
`coach.md` and `context_window.md` were also moved into `.claude/skills/coach/SKILL.md` and `.claude/skills/context-window/SKILL.md` on disk, but are intentionally left uncommitted (untracked) — their content still needs the same review pass already done for the other three skills before it enters git history.

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
