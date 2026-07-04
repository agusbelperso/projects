# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is not an application codebase — there is no build, lint, or test tooling, and none should be assumed or invented. It's a personal library of Claude Code skill/prompt definitions, each a standalone markdown file at the repo root. Each file (except `README.md`) follows the skill convention: YAML frontmatter with `name`/`description` (used for skill discovery and invocation) followed by the instructions Claude should execute when the skill is invoked.

## Hard constraints (apply across all skills in this repo)

- Do not assume or guess intent. If a requirement is ambiguous, pause and ask for clarification — use the flow in `prompt-builder.md`.
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

- **`prompt-builder.md`** — asks the user four fixed questions (role, task, context, format), assembles a clean prompt, and always appends a fixed "ask if unclear" closing line. Referenced by the ambiguity policy above as the fallback for underspecified requests.
- **`coach.md`** — a workflow-coaching skill that scores a session against five dimensions (safety discipline, workflow leverage, prompt clarity, output discipline, continuous improvement) and appends results to a coaching log. It reads two hardcoded absolute paths — `~/.claude/projects/-Users-abeltraminopersoglia-repos/memory/coaching_observations.md` and `~/.claude/CLAUDE.md` — under a **different username** (`abeltraminopersoglia`) than the current machine's home directory (`agustina`). Treat these paths as possibly stale; verify they exist before relying on them, and if they don't resolve, ask the user for the correct paths rather than guessing. Step 5 chains into an external `10x-coach` skill for cross-referencing.
- **`context_window.md`** — computes current session token usage as a percentage of a 200k context limit by summing usage fields from the live session's `.jsonl` transcript under `~/.claude/projects/*/`.  Falls back to a `~/.claude/usage.db` SQLite query if the transcript isn't found.
- **`log-this.md`** — logs a completed session to `~/.claude/dashboard-data.jsonl` in a fixed schema (speed/cost/quality/simplicity/adoption metrics). Shares the same token-summation logic as `context_window.md` and pulls from the same session JSONL. Null fields should be left null, never guessed.
- **`push.md`** — the commit/push checklist for *this* repo. Enforces: confirm ownership of any pre-staged files before including them, mandatory `CHANGELOG.md` entry before every commit, explicit file staging (never `git add -A`/`.`), a `type(scope): subject` commit convention (scope = the skill file touched, or `meta` for repo-level files), and a Claude co-author trailer.

## Working in this repo

- Changes here are almost always edits to a single skill's markdown instructions — there's no cross-file dependency graph to trace beyond the shared token-usage snippet duplicated in `context_window.md` and `log-this.md`, and the shared coaching paths in `coach.md`.
- When adding a new skill file, follow the existing frontmatter convention (`description:` at minimum; `name:` if the skill needs a stable identifier) so it stays consistent with the others.
