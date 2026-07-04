---
name: log-this
description: Log the current session to ~/.claude/dashboard-data.jsonl following the 10x-coach Mode 2 format. Auto-pulls token usage from the session JSONL transcript, asks three questions, then writes the entry.
---

# Log This Session

You are logging a completed workflow to the 10x-coach dashboard. Follow these steps in order.

## Step 1 — Pull token usage automatically

Read the session JSONL transcript to sum token usage. The transcript path is shown in the conversation context (look for a `.jsonl` path under `~/.claude/projects/`). Run:

```bash
cat "<session>.jsonl" | python3 -c "
import sys, json
input_tokens = output_tokens = cache_read = cache_write = turns = 0
for line in sys.stdin:
    try:
        obj = json.loads(line)
        usage = obj.get('usage') or obj.get('message', {}).get('usage', {})
        if usage:
            input_tokens  += usage.get('input_tokens', 0)
            output_tokens += usage.get('output_tokens', 0)
            cache_read    += usage.get('cache_read_input_tokens', 0)
            cache_write   += usage.get('cache_creation_input_tokens', 0)
            turns         += 1
    except: pass
print(f'input_tokens:  {input_tokens}')
print(f'output_tokens: {output_tokens}')
print(f'cache_read:    {cache_read}')
print(f'cache_write:   {cache_write}')
print(f'turns:         {turns}')
"
```

If the transcript path is not visible in context, fall back to the SQLite DB:

```bash
sqlite3 ~/.claude/usage.db "
SELECT session_id, SUM(input_tokens), SUM(output_tokens), SUM(cache_read_tokens), SUM(cache_creation_tokens), COUNT(*)
FROM turns
WHERE session_id = (SELECT session_id FROM turns ORDER BY id DESC LIMIT 1)
GROUP BY session_id;
"
```

Record `input_tokens`, `output_tokens`, `cache_read_tokens`, `cache_write_tokens`, and `turns`. Do not collapse into a single total — the four types have very different cost profiles and are not comparable.

## Step 2 — Ask three questions in a single message

Ask the user:

1. **Value delivered** — what measurable impact did this have? (time saved, risk reduced, quality improved, steps eliminated — even a rough estimate counts)
2. **Skill or direct prompt?** — was a named skill invoked (e.g. analyze, five-fifteen, coach)? If yes, which one?
3. **Steps before vs. after** — how many manual steps did this replace? Rough is fine.

Wait for answers before proceeding.

## Step 3 — Build the JSONL entry

Use today's date and compute the ISO week (YYYY-WNN).

Populate all fields honestly — leave null if unknown rather than guessing:

```json
{
  "date": "YYYY-MM-DD",
  "week": "YYYY-WNN",
  "workflow": "short name for what was done",
  "objective": "what this achieved in one sentence",
  "skill_used": true or false,
  "skill_name": "skill name or null",
  "speed": { "cycle_time_before": null, "cycle_time_after": null },
  "cost": { "manual_hours_saved": null, "token_cost": null, "input_tokens": null, "output_tokens": null, "cache_read_tokens": null, "cache_write_tokens": null, "turns": null },
  "quality": { "error_rate_before": null, "error_rate_after": null, "follow_ups_needed": null },
  "simplicity": { "steps_before": null, "steps_after": null, "tool_calls": null },
  "ai_adoption": { "percent_automated_with_review": null },
  "wins": "what went well",
  "risks": "blockers or concerns",
  "next_automation_candidate": "what to automate next",
  "value_impact": "Metric: before -> after (change)"
}
```

`token_cost` stays null (subscription plan — no per-session cost). `tokens_used` and `turns` come from Step 1.

## Step 4 — Append to the dashboard file

Do not build this line with `echo` and a shell-quoted JSON string — free-text fields (`wins`, `risks`, `next_automation_candidate`, `value_impact`) will often contain apostrophes and break shell quoting. Instead, write the entry as a Python dict literal and let `json.dumps` handle escaping:

```bash
python3 -c "
import json
entry = {
    'date': 'YYYY-MM-DD',
    # ...rest of the fields from Step 3...
}
with open('$HOME/.claude/dashboard-data.jsonl', 'a') as f:
    f.write(json.dumps(entry) + '\n')
"
```

Confirm it was written with `tail -1 ~/.claude/dashboard-data.jsonl`.

## Step 5 — Confirm

Tell the user: entry logged, week it landed in, and the token/turn totals captured for this session.
