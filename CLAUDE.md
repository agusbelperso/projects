# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is not an application codebase — there is no build, lint, or test tooling, and none should be assumed or invented. It's a personal library of Claude Code skills. Each skill lives at `.claude/skills/<name>/SKILL.md`, the convention Claude Code requires for a skill to be reachable via `/<name>`. Each `SKILL.md` follows the skill convention: YAML frontmatter with `name`/`description` (used for skill discovery and invocation) followed by the instructions Claude should execute when the skill is invoked.

## Hard constraints (apply across all skills in this repo)

- Do not assume or guess intent. If a requirement is ambiguous, pause and ask for clarification — use the flow in `/prompt-builder`.
- Do not execute active/destructive actions without prior human approval via an explicit review loop.
- Prioritize safety and clarity over speed.

## Human-in-the-loop (HITL) triggers

Pause execution and ask for confirmation BEFORE:
- **File overwrites** — modifying existing logic or refactoring core architecture components.
- **Package management** — installing, upgrading, or removing external dependencies or libraries.
- **Destructive actions** — deleting files, modifying databases, or clearing persistent caches.
- **External calls** — making active API calls to external services, or running multi-file global find-and-replace routines.

## Clarification principles

- **Ambiguity policy**: if an instruction has multiple valid technical interpretations, list the top 2 alternatives and ask the user to choose.
- **Missing context**: if a local variable, configuration setting, or architectural pattern is unclear, don't invent dummy data — stop and query the user.
- **Impact assessment**: before proposing major active changes, give a brief 2-bullet summary of *what* will change and *why* it requires approval.

## Skill inventory and how they interconnect

- **`/prompt-builder`** (`.claude/skills/prompt-builder/SKILL.md`) — asks the user four fixed questions (role, task, context, format), assembles a clean prompt, and always appends a fixed "ask if unclear" closing line. Referenced by the ambiguity policy above as the fallback for underspecified requests.
- **`/log-this`** (`.claude/skills/log-this/SKILL.md`) — logs a completed session to `~/.claude/dashboard-data.jsonl` in a fixed schema (speed/cost/quality/simplicity/adoption metrics), pulling token usage from the session's `.jsonl` transcript. Null fields should be left null, never guessed.
- **`/push`** (`.claude/skills/push/SKILL.md`) — the commit/push checklist for *this* repo. Enforces: confirm ownership of any pre-staged files before including them, mandatory `CHANGELOG.md` entry before every commit, explicit file staging (never `git add -A`/`.`), a `type(scope): subject` commit convention (scope = the skill touched, or `meta` for repo-level files), and a Claude co-author trailer.
- **`/data-analysis`** (`.claude/skills/data-analysis/SKILL.md`) — end-to-end data analysis assistant (Excel/CSV, business metrics, ROI, HTML report generation, PPTX export), ported from an external reference remote. Has its own `references/` and `scripts/` subdirectories with self-installing Python/Node dependencies.
- **`/operations-manager`** (`.claude/skills/operations-manager/SKILL.md`) — workforce planning, process optimization, compliance management, facilities coordination, and operational efficiency (Lean Six Sigma, PDCA, capacity planning). Scripts: `capacity_planner.py`, `process_mapper.py`, `sla_tracker.py`. Has one real reference doc, `references/cost_optimization.md` (a bottom-up strategic HR cost management model); `references/process_design.md`, `lean_operations.md`, and `vendor_management.md` are still referenced by the SKILL.md but don't exist. Adopted individually from the `claude-skills` reference remote's `hr-operations` bundle (2 of its 4 skills — `hr-business-partner` and `talent-acquisition` were not adopted).
- **`/people-analytics`** (`.claude/skills/people-analytics/SKILL.md`) — workforce analytics, attrition modeling, engagement analysis, compensation benchmarking, and DEI metrics. Scripts: `attrition_predictor.py`, `headcount_planner.py`, `survey_analyzer.py`. Has one real reference doc, `references/hr_metrics.md` (HR cost-forecasting dashboard/KPI reference); `references/predictive_models.md`, `survey_design.md`, and `data_ethics.md` are still referenced by the SKILL.md but don't exist. Provides the data foundation `/operations-manager` draws on for process/compliance decisions — use `/operations-manager` for process execution and compliance, `/people-analytics` for the underlying workforce data and metrics.
- **`/financial-analyst`** (`.claude/skills/financial-analyst/SKILL.md`) — financial ratio analysis, DCF valuation, budget variance analysis, and rolling forecast construction. Scripts: `ratio_calculator.py`, `dcf_valuation.py`, `budget_variance_analyzer.py`, `forecast_builder.py`. Has working `references/` (ratios guide, valuation methodology, forecasting best practices) and `assets/` (report templates, sample data, expected output) — all 3 referenced files actually exist, unlike the hr-operations skills. Also adopted from the `claude-skills` reference remote, from its `finance` bundle (1 of 3 skills there — `business-investment-advisor` and `saas-metrics-coach` were not adopted).

Note: `/coach` and `/context-window` are **not** part of this repo — they were generalized and moved to the global `~/.claude/skills/` so they work across every repo, not just this one. See `~/.claude/skills/coach/SKILL.md` and `~/.claude/skills/context-window/SKILL.md` directly if you need to look at them.

## Working in this repo

- Changes here are almost always edits to a single skill's `SKILL.md` — there's no meaningful cross-file dependency graph within this repo to trace.
- New skills must live at `.claude/skills/<name>/SKILL.md` to be reachable via `/<name>` — a loose `.md` at the repo root will not be picked up. Follow the existing frontmatter convention (`name:` and `description:`) so it stays consistent with the others.
- Not every personal skill belongs in this repo: if a skill isn't specific to this repo's own workflow (push/prompt-builder) and should work everywhere, it belongs in `~/.claude/skills/` instead.
