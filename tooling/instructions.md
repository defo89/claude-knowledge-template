# Knowledge repo (long-term memory)

A git repo of Markdown notes holds what earlier sessions learned. Its path is on the "Knowledge repo" line of the user CLAUDE.md, next to the import of this file. It is the source of truth for past findings. Treat every note as point-in-time: check `as_of` and verify before acting on anything that can change.

The repo has `findings/` (one durable fact per note), `projects/` (status of ongoing efforts) and a generated `INDEX.md` (one line per note).

- **Recall.** Before digging into an error, tool, repo, system or topic that may have come up before, search the repo. Grep `findings/` and `projects/` for identifiers, error strings and component names plus a synonym or two, or skim `INDEX.md`. Say which notes you used and their `as_of`.
- **Save.** Use the `remember` skill for knowledge worth keeping: a verified fact, a decision and its reason, a gotcha, a command that worked, project status. Offer it when an investigation ends with something reusable. Save without asking only when the user tells you to remember something, and at a hand-off.
- **Hand off.** When a chunk of work ends (a PR pushed, a review requested, waiting on the user or someone else) or a break of more than an hour is coming, hand off in one reply instead of only suggesting it. Every call re-reads the whole conversation, and the first call after an hour idle writes all of it to the cache again, so a long session costs many times more than a fresh one (`findings/claude-code-token-usage.md`).
    1. Save the state with the `remember` skill, without asking: the project note (current state, open items, and the next step with the commands or links to check it), plus any finding the work produced. Stop background tasks you started, or note them in the project note.
    2. Ask the user to run `/clear`; Claude can't run it. Say how big the context is, in tokens and in % of the context window, as the repo's `tooling/claude-context` reports it.
    3. End with a continuation prompt in a code block, ready to paste into the fresh session: the note to read, and the next step. Copy it to the clipboard too (pipe it without a trailing newline to `pbcopy` on macOS, `wl-copy` or `xclip -selection clipboard` on Linux, or `clip.exe` on Windows), so the user only has to paste it.
- **No secrets** in the repo (tokens, passwords, keys, kubeconfigs, auth headers). Note where a secret lives, never its value.
- **Auto memory** (`MEMORY.md`) holds only how to work with the user plus one-line pointers into the repo. Findings and project logs go into the repo.
