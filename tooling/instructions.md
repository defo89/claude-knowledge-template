# Knowledge repo (long-term memory)

A git repo of Markdown notes holds what earlier sessions learned. Its path is on the "Knowledge repo" line of the user CLAUDE.md, next to the import of this file. It is the source of truth for past findings. Treat every note as point-in-time: check `as_of` and verify before acting on anything that can change.

The repo has `findings/` (one durable fact per note), `projects/` (status of ongoing efforts) and a generated `INDEX.md` (one line per note).

- **Recall.** Before digging into an error, tool, repo, system or topic that may have come up before, search the repo. Grep `findings/` and `projects/` for identifiers, error strings and component names plus a synonym or two, or skim `INDEX.md`. Say which notes you used and their `as_of`.
- **Save.** Use the `remember` skill for knowledge worth keeping: a verified fact, a decision and its reason, a gotcha, a command that worked, project status. Offer it when an investigation ends with something reusable. Save without asking only when the user tells you to remember something.
- **No secrets** in the repo (tokens, passwords, keys, kubeconfigs, auth headers). Note where a secret lives, never its value.
- **Auto memory** (`MEMORY.md`) holds only how to work with the user plus one-line pointers into the repo. Findings and project logs go into the repo.
