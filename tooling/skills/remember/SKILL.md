---
name: remember
description: Save durable knowledge from the current session (a verified finding, a decision and its reason, a gotcha, a command that worked, or project status) as a Markdown note in the user's knowledge repo, updating an existing note instead of duplicating it, then commit and push. Use when the user says "remember this", "save this", "note this down" or "/remember", or accepts an offer to save something.
---

# remember

The knowledge repo's path is on the "Knowledge repo" line of the user CLAUDE.md (written by the repo's `tooling/install`). Below, `<repo>` stands for that path.

## 1. Pick what to save

If the user said what to remember, save that. Otherwise list up to five candidates from this session, one line each, and save the ones the user picks.

A good note holds one durable fact someone will want months from now: what is true, how it was verified, and when it matters. It is not a session narrative, so leave out intermediate commits, comment IDs and step-by-step history unless they are the point.

`type` is one of:

- `finding`: verified behavior, cause, limit or gotcha
- `decision`: a choice that was made, and why
- `howto`: a procedure or command that worked
- `reference`: where something lives (repos, docs, dashboards, owners)
- `project`: status of an ongoing effort. Project notes go in `projects/`, all other types in `findings/`.

## 2. Keep it clean

Never write secrets (tokens, passwords, keys, kubeconfigs, auth headers). Write where the secret lives instead. `<repo>/tooling/blocked-patterns.txt` lists anything else the user never wants committed; the pre-commit hook enforces it.

## 3. Update before you create

Search the repo for the key identifiers and a synonym or two:

```bash
grep -ril -e '<term>' -e '<synonym>' <repo>/findings <repo>/projects
```

- The same fact is already there: update that note (add the new source, correct details, bump `as_of`).
- The fact has changed: rewrite the note to the new truth and say what changed. If the old version must stay visible, set its `status: superseded` and `superseded_by: <new-slug>`, then create the new note.
- Related notes: link them both ways with `[[slug]]`.

## 4. Write the note

Path: `<repo>/findings/<slug>.md` or `<repo>/projects/<slug>.md`. The slug is short, lowercase and hyphenated, names the topic, and has no date (for example `ci-runner-cannot-reach-internal-docs`).

```markdown
---
title: "<the claim in one line, specific enough to act on>"
type: finding
tags: [<topic>, <system>]
as_of: <YYYY-MM-DD>
status: current
sources:
  - <URL, issue, PR, doc, file path or command that shows it>
---

<The fact in one to three sentences.>

**Evidence:** <how it was verified>

**How to apply:** <when it matters and what to do>

Related: [[<slug>]]
```

Keep `title` in double quotes, with no double quotes inside it.

A `project` note names the effort in `title`, then gives the goal, links (issues, PRs), current state and open items, each with dates. Open items include the next step, with the commands or links to check it, so a fresh session can continue from the note alone. Rewrite the state and open items as they change instead of appending a diary. Move finished detail into dated history bullets, and pull reusable lessons out into findings.

## 5. Commit and push

```bash
git -C <repo> add -A
git -C <repo> commit -m "<type>: <title>"
git -C <repo> push
```

The pre-commit hook regenerates `INDEX.md`, scans for secrets and rejects anything in `tooling/blocked-patterns.txt`. If it blocks the commit, fix the note. Never bypass it with `--no-verify` unless the user says so. If the push fails (offline, VPN, credentials), say so and keep the local commit.

Finish with one line per note: path, title, and whether it was pushed. If the save is part of a hand-off, then ask the user to run `/clear` and end with a continuation prompt in a code block: the note to read, and the next step.
