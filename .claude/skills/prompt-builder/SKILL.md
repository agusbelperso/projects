---
name: prompt-builder
description: Build a clean, complete prompt by asking the user for role, task, context, and format up front, then assembling it with a fixed clarification closing line. Use when a request is underspecified or the user wants help drafting a prompt.
---

# Prompt Builder

Ask the user all four questions at once in a single message, formatted as a numbered list:

1. **Role** — Who should Claude be? (e.g. "a senior data analyst", "a concise editor", "a SQL expert")
2. **Task** — What do you need done? (verb + object, be specific)
3. **Context** — What background, constraints, or files are relevant?
4. **Format** — How should the output look? (e.g. bullet list, SQL query, plain prose, table, max length)

Wait for the user's answers. Then assemble a clean, complete prompt using their inputs and append this closing line verbatim at the end — always, without modification:

> If there's anything that's not clear, if you are missing context or anything seems lacking in my response, TELL ME, ask me, do not guess or assume.

Show the assembled prompt to the user inside a markdown code block so it's easy to copy. Then ask: "Want to adjust anything?"
