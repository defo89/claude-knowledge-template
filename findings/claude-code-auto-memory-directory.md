---
title: "Claude Code auto memory is per starting folder, unless autoMemoryDirectory points every session at one folder"
type: finding
tags: [claude-code, memory, settings]
as_of: 2026-09-28
status: current
sources:
  - Claude Code settings reference, autoMemoryDirectory
  - claude -p checks started in two different folders, 2026-09-28
---

By default auto memory lives in `$CLAUDE_CONFIG_DIR/projects/<sanitized-cwd>/memory/`, so every starting folder gets its own `MEMORY.md`: a session started in one repo doesn't see what was saved from another.

The `autoMemoryDirectory` setting (supports `~/`) points every session at one folder. It is ignored in a checked-in project `.claude/settings.json`, so set it in the user `settings.json`; `tooling/install` does that.

**Evidence:** with `autoMemoryDirectory` set, fresh `claude -p` sessions started in two different repos reported the same memory directory and loaded the same index.

**How to apply:** keep that shared index short, preferences and one-line pointers only, because it loads into every session.

Related: [[claude-code-skills-folder-needs-restart]]
