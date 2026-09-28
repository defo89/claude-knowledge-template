---
title: "A running Claude Code session only picks up new skills from skill folders that existed when it started"
type: finding
tags: [claude-code, skills]
as_of: 2026-09-28
status: current
sources:
  - claude -p ok --output-format stream-json --verbose (startup init event), 2026-09-28
---

A skill linked into an existing skills folder showed up in a running session. A session that had started before its config's `skills/` folder existed did not offer the new skill, while every fresh session did. The likely mechanism is that a session only watches skill folders that existed when it started; that is inferred from this difference, not documented.

**Evidence:** the startup `init` event of fresh sessions listed the skill under `skills` and `slash_commands`; the older session never offered it.

**How to apply:** after installing the first user skill, restart running sessions (`claude --resume` starts a new process, so it should pick the skill up too). To see what a session registers, read the init event instead of asking the model, which may answer yes just because its instructions mention the skill:

```sh
claude -p ok --output-format stream-json --verbose | jq -c 'select(.subtype=="init") | .skills'
```

Related: [[claude-code-auto-memory-directory]]
