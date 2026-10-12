---
title: "Claude Code usage grows with context size, not output: every call re-reads the whole conversation, and the first call after an hour idle writes all of it to the cache again"
type: finding
tags: [claude-code, tokens, prompt-caching, usage-limits]
as_of: 2026-10-12
status: current
sources:
  - Claude Code transcripts (~/.claude*/projects/*/*.jsonl, the usage of each assistant message)
  - tooling/claude-usage 2026-10-08, run 2026-10-12 (cache reads priced per model)
  - Claude API prompt-caching reference: cache read 0.05x input on Claude Opus 5.5 ($0.20 against $4 per MTok), writes 1.25x (5 min) or 2x (1 h)
---

Every API call in a Claude Code session sends the whole conversation again: the API keeps no session, and the prompt cache only spares the server from processing the matching prefix again. That turns the resent prefix into a cache read, cheaper than fresh input but still counted, so a session grown to ~0.9M tokens re-reads ~0.9M tokens on every tool call. The cache expires after an hour: the first call after a longer break writes the whole context to the cache again. With a 1M context window, auto-compaction starts only near 1M, so nothing caps this. Output, thinking included, is a small share.

**Evidence:** `tooling/claude-usage 2026-10-08`, run after the day ended and counting each API call once across profiles: one working day of three long-lived sessions on Opus 5.5 under a claude.ai plan:

| Session | Open for | Calls | Avg context per call | Cache reads | Output |
|---|---|---|---|---|---|
| A | 2 weeks | 83 | 785k | 63M | 199k |
| B | 1 week | 60 | 806k | 46M | 81k |
| C | 3 days, compacted twice | 178 | 250k | 43M | 204k |

- Day total: 153M cache-read tokens, 5.4M cache-write tokens, 484k output tokens. The largest tool result was ~4k tokens.
- 4.6M of the 5.4M cache writes were six full re-caches of 0.3–0.9M tokens each, every one the first call after 3 hours to 7 days idle. Three came within two minutes: all three sessions were resumed after the same 3-hour break, and each paid in full.
- The cache was the 1-hour one: since October almost every cache write in both profiles was 1-hour. In August and September many were 5-minute, which expire after five idle minutes.
- Session C made three times as many calls as B and re-read less.
- At Claude Opus 5.5 API prices (cache read $0.20, 1-hour cache write $8 and output $20 per MTok against $4 for input: 0.05, 2 and 5 times an input token), cache writes were ~52% of the day (the re-caches alone ~44%), cache reads ~37% and output ~12%. With the general 0.1 read ratio of other models, reads would lead at ~54%. How a claude.ai plan weighs token types is not published.
- Over the previous three weeks, the first call of a fresh session sent ~37k tokens (median of 71: system prompt, tools, CLAUDE.md, MEMORY.md), and the first call after a compaction ~59k (median of 71).

**How to apply:**
- Use one session per chunk of work: until a PR is pushed, a review is requested or you are waiting on someone. Then update the project note and `/clear`. A fresh session that reads a ~3k-token project note starts at ~40k instead of ~900k.
- `/remember` alone saves nothing; the saving comes from the `/clear` after it.
- Before a break of more than an hour, hand off and clear. Resuming a big session costs a full re-cache, and every big session open in parallel pays it. At Opus 5.5 API prices one re-cache of an ~800k-token session ($6.45) costs about as much as 30 of its normal calls (~$0.21 each).
- If a session has to go on, `/compact` early (~150–200k) instead of waiting for auto-compaction near 1M. A smaller context window compacts earlier on its own.
- Lowering effort trims only output, the smallest share.
- Measure any day: `tooling/claude-usage [YYYY-MM-DD]`.

Related: [[claude-code-auto-memory-directory]]
