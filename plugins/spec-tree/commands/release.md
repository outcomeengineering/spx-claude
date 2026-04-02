---
description: Reflect, persist, and close session without creating a handoff file
allowed-tools:
  - Skill
---

Invoke `/handoff --no-session $ARGUMENTS`.

This is a convenience alias. `/release` runs the full reflection and persistence protocol — anchoring nodes, five perspectives, persistence decisions — but skips session file creation. Use when the work is complete or durable persistence is sufficient for future agents to reconstruct context.
