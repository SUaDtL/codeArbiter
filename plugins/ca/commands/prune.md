---
description: Trim clutter from the session transcript to extend its usable lifetime — analyze, prune a copy, or toggle the after-each-turn service. Dry-run by default.
argument-hint: status | dry | run <path> | audit <path> | on | off
---

# /ca:prune — session transcript pruner

Long sessions die when the transcript JSONL fills the context window with clutter — bulky
`toolUseResult` sidecars, oversized tool outputs, thinking blocks, MCP/shell noise, stale file
reads. The pruner trims that clutter while treating transcript integrity as sacred: it never edits
bytes (untouched lines, including unknown types, are re-emitted verbatim), it preserves every line
and the `uuid`/`parentUuid` chain, and it keeps the most recent turns verbatim. The backing tool is
`${CLAUDE_PLUGIN_ROOT}/hooks/prune-transcript.py`; the engine is `_prunelib.py`.

## The honest limitation — read this first

Trimming the on-disk transcript does **not** shrink a *live* session's in-memory context. The
running CLI sends its in-memory history to the API; the JSONL is a write-only log it does not
re-read to build the next request. So the gains land at **`claude --resume` / restart** and at the
**next compaction** — not on the current turn. The service mode keeps the transcript continuously
lean so every resume starts lean.

## Argument

`$ARGUMENTS` is one of:

- `status` (default) — report the prune state for this session from
  `~/.codearbiter/prune-state.json` (cumulative reduction, last-run age) and whether the service is
  `on`/`dry`/`off` (`CODEARBITER_PRUNE`).
- `dry` — run a dry-run analysis on a **copy** of this session's transcript and present the
  per-strategy reduction table. Never writes.
- `run <path>` — prune the given transcript with `--execute`. Targets a **copy or an old/inactive
  transcript only**; the tool refuses a live transcript.
- `audit <path>` — read-only integrity report (parse, orphans, tool-pair coverage, markers).
- `on` / `off` — guidance on enabling the after-each-turn service (set `CODEARBITER_PRUNE=on`, or
  `dry` to log without writing).

## Procedure

1. **status / dry / audit** — run the backing tool and present its output verbatim, e.g.
   `python3 "${CLAUDE_PLUGIN_ROOT}/hooks/prune-transcript.py" audit <path>`. For `dry`, copy the
   live transcript to a scratch path first (`<path>.copy.jsonl`) and analyze the copy.
2. **run** — confirm the path is a copy or an inactive session, then
   `python3 "${CLAUDE_PLUGIN_ROOT}/hooks/prune-transcript.py" <path> --execute [--tier T]`.
   Present the reduction and the post-run `audit`.
3. **on/off** — explain the service: the `UserPromptSubmit` and `PreCompact` hooks prune the live
   session at safe points (never blocking the prompt; always exiting 0). Tiers: `gentle` (sidecar +
   oversize clamp), `standard` (+ reasoning fold, aged/MCP/shell), `aggressive` (+ stale-read,
   reminder dedup, image evict). Config via `CODEARBITER_PRUNE`, `CODEARBITER_PRUNE_TIER`,
   `CODEARBITER_PRUNE_KEEP_RECENT`, `CODEARBITER_PRUNE_MAXBYTES`.

## Hard rules

- MUST NOT run `--execute` against the **live** session's transcript directly — that path belongs to
  the hook (the tool refuses a recently-modified file). Manual runs target copies or old sessions.
- MUST present the resume-only-gains limitation when a user expects a live-context drop.
- The service ships **off**; enabling it is the user's choice.

## When NOT to use

When the session is short or the context bar is nowhere near compaction — pruning a small transcript
saves little and the most recent turns are protected anyway.
