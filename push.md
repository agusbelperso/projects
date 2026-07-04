---
description: Commit and push changes to this repo — enforces CHANGELOG update, safe file staging, and correct commit conventions.
---

# Push

Before committing anything to this repo, follow these steps in order. Do not skip any.

## Step 1 — Check what's changed

Run `git status` and `git diff --stat` from the repo root. Identify:
- **Already-staged files** (`Changes to be committed`) — these may have been staged by a different chat session. Show them to the user and ask explicitly: "These files are already staged — were they from your current work or a previous session? Should I include them, unstage them, or commit them separately?"
- Modified files to stage
- Untracked files — decide whether each belongs in the commit (see exclusions below)

Do not assume pre-staged files belong in the current commit. Always confirm with the user first.

**Never stage:**
- `.DS_Store` or other OS/editor junk files
- Empty files
- Any `.env`, credentials, or token files
- Anything in `/tmp` or scratch output directories

## Step 2 — Update CHANGELOG.md

**This is mandatory. Do not commit without it.**

Open `CHANGELOG.md` (repo root). Add a new `## YYYY-MM-DD` entry at the top (below the header). Use today's date.

Structure:
```
## YYYY-MM-DD (short description of what changed)

### Added
- `path/to/file` — what it does and why it was added

### Changed
- `path/to/file` — what changed and why

### Context
One paragraph explaining the "why" — what problem this solves, what state the repo is now in.
```

Rules:
- Every file in the commit gets a bullet
- Be specific: don't write "updated coach.md" — write what changed and why
- The Context section should be useful to someone reading the log months later

## Step 3 — Stage files

Stage specific files by name. Do not use `git add -A` or `git add .`.

Always include `CHANGELOG.md` in the staged set.

## Step 4 — Commit

Format: `type(scope): short description`
- Types: `feat`, `fix`, `docs`, `refactor`, `chore`
- Scope: the skill file affected (e.g. `coach`, `push`, `prompt-builder`, `log-this`, `context-window`), or `meta` for repo-level files (`CLAUDE.md`, `CHANGELOG.md`, `README.md`)
- Subject line: ≤72 characters, imperative mood
- Body: bullet points for non-obvious details if needed

Always add the co-author trailer:
```
Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

## Step 5 — Push

```bash
git push origin main
```

Confirm the push succeeded and show the user the commit hash.
