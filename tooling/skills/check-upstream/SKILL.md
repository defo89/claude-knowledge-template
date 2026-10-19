---
name: check-upstream
description: Check the upstream template repo (github.com/videlov/claude-knowledge-template) for updates to tooling, hooks, skills, and structure. Use when the user says "check upstream", "check for updates", "sync with upstream", or "/check-upstream".
---

# Check Upstream

Compare this knowledge repo's tooling against the upstream template and selectively apply updates.

## How it works

`tooling/check-upstream` is a bash script that:
1. Downloads the upstream tarball (no auth needed — the template is public)
2. Reads `tooling/.upstream-base` to know which upstream commit the local copy was last synced to
3. For each trackable file, classifies it: up-to-date, changed upstream only, changed locally only, changed on both sides, new, or deleted upstream
4. In apply mode, uses `git merge-file` for three-way merges so local adaptations are preserved

## Steps

1. **Run the summary**

```bash
<repo>/tooling/check-upstream
```

Show the output to the user. It prints a table of files grouped by status.

2. **Walk through the results with the user**

For each group, offer actions:

- **Changed upstream only**: safe to apply. Ask: apply all, or review individually?
- **Changed on both sides**: will get conflict markers. Ask: apply (with conflicts to resolve), skip, or show diff first?
- **New upstream**: ask: apply or skip?
- **Deleted upstream**: ask: remove locally or keep?
- **Changed locally only / up-to-date**: no action needed.

3. **Apply chosen updates**

```bash
<repo>/tooling/check-upstream --apply
```

This writes merged files and updates `tooling/.upstream-base`.

4. **Handle conflicts**

If any files have conflict markers, show them to the user and help resolve them.

5. **Commit**

After the user is satisfied, stage and commit:

```bash
git -C <repo> add -A
git -C <repo> commit -m "chore(tooling): sync with upstream template"
```

Remind the user to test: run `tooling/install` and verify hooks.

## Safety

This skill pulls updates from the public upstream template. It never sends local content upstream.

### NEVER do:

- `gh api` write operations (PUT, POST, PATCH) against the upstream repo
- Create PRs, issues, or forks on the upstream repo
- Expose local file contents to any external API

### Safe to do:

- Read upstream content (curl GET on the public repo)
- Write to the local repo (with user approval)
- Commit and push to the user's own origin (this is normal — `/remember` does the same)

## Notes

- No `gh` CLI or GitHub auth required — uses `curl` on the public repo.
- The upstream repo is not added as a git remote. All fetching uses HTTPS downloads to `/tmp/`.
- `tooling/.upstream-base` tracks the last-synced upstream commit. On first run (no base file), three-way merge is unavailable and files are reported as "changed (no base)".
- `findings/claude-code-token-usage.md` is tracked because it ships with the template and is referenced by `tooling/instructions.md` and the README.
- After applying updates, remind the user to test the tooling (`tooling/install`, verify hooks).
