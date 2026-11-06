# 03 — Speculative Edit Decoding & Latency

Goal: understand why Cursor/Zeta feel “instant” and how to engineer that yourself.

Quality without latency still feels dumb. Latency without quality feels broken. Tab needs both.

## Why Tab is a weird LLM workload

Classic chat:

- Medium prompt
- Long generation
- User waits for a paragraph

Tab / edit prediction:

- **Huge prompt** (file window + history + related files + diagnostics)
- **Short generation** (often tens to a few hundred tokens)
- Fired on **every keystroke**
- Stale results must be **canceled**
- Many requests should return **nothing** (`NO_EDITS`)

So you are prefill-bound and cancel-heavy. Optimize accordingly.

## Latency budget (targets)

| Stage | Budget (feel-good) |
|-------|--------------------|
| Client debounce | 20–80ms after pause-in-burst |
| Context pack | <10–30ms local |
| Network RTT | local UNIX sock / localhost ideal |
| Prefill + first tokens | start painting <150–250ms |
| Full suggestion | <300–500ms common case |
| Cancel propagation | immediate on new keystroke |

If p95 is multi-seconds, users will prefer dumb LSP completions.

## Two families of speculation

### A) Classic speculative decoding (draft + verify)

Small draft model proposes tokens; large model verifies in parallel.

Useful, but Tab has a stronger prior…

### B) Code-as-draft / edit speculation (the Tab trick)

When the model rewrites a region, **most tokens equal the input region**.

So:

1. Treat the current editable region as a **draft token sequence**.
2. Feed chunks of that draft as speculated outputs.
3. Model accepts unchanged spans in bulk (cheap).
4. At divergence points, generate truly new tokens.
5. After the edit island, resync and speculate again on the remainder.

Zed described a variant using **n-gram search** over the input to find jump-off points for parallel generation.

Cursor’s public engineering writeups describe the same intuition: the file is the draft; stream diffs as the model diverges.

**This is why rewrite-region formats can be faster than regenerating a whole file, and why structured search-replace can be even fewer tokens.**

## Output formats vs speed

| Format | Tokens out | Parse complexity | Multi-location edits |
|--------|------------|------------------|----------------------|
| FIM middle | Fewest | Easy | Weak (cursor only) |
| Search-replace patches | Few | Medium | Good |
| Editable-region rewrite | Medium (mostly copy) | Medium | Good in-window |
| Full-file rewrite | Huge | Easy | Best but too slow |

SOTA Tab systems usually pick **rewrite window** or **patches**, then accelerate with speculation / early exit.

`NO_EDITS` should be the fastest path (tiny generation or classifier).

## Serving stack checklist

1. **Prefix caching** — consecutive keystrokes share almost the same prompt prefix; cache KV for stable prefix segments (history may shift — design packing so stable parts come first).
2. **Prompt packing order** — put rarely changing context first (related files), volatile cursor excerpt last, to maximize cache hits when possible.
3. **Continuous batching** — vLLM / SGLang / TensorRT-LLM.
4. **Quantization** — FP8/INT4 students; measure accept-rate delta, not just perplexity.
5. **Stop tokens** — end markers for region / `NO_EDITS` so you don’t over-generate.
6. **Max tokens** — hard cap (Zed defaults often ~256–512); oversize predictions are UX-negative anyway.
7. **Sticky routing** — same user session → same replica for cache warmth.
8. **Cancel** — HTTP disconnect / generation abort hooked to client cancellation.
9. **Speculative decode** — draft model or code-as-draft.
10. **Local first for R&D** — eliminate network before blaming the model.

## Client-side latency engineering (Zed)

Even a perfect server fails if the client is naive.

Implement / verify:

- **Debounce while typing bursts**, predict on micro-pauses
- **Single-flight**: new request cancels old
- **Don’t flash** intermediate wrong ghosts (wait for parse-valid result)
- **Prefetch** on cursor jump / accept (next prediction likely)
- **Reuse context** computation across keystrokes (diff the excerpt)
- **Eager vs subtle mode** — subtle reduces visual noise when LSP menu fights you
- Stream tokens → incremental diff only if parse-safe; otherwise wait for complete region

## Streaming diffs

Ideal UX:

1. Tokens arrive
2. Parser maintains a partial rewritten region
3. UI updates ghost hunks live
4. On cancel, ghost clears without applying

Hard parts: incomplete markers, malformed fences, mid-token Unicode. Start with **wait-for-complete-region**, then add streaming once stable.

## Measuring the right thing

Log per request:

- `pack_ms`, `ttft_ms`, `total_ms`
- `prompt_tokens`, `cached_prompt_tokens`, `gen_tokens`
- `canceled: bool`
- `shown: bool`, `accepted: bool`
- `no_edits: bool`
- `provider`, `model_version`

Dashboards:

- TTFT p50/p95 for **shown** suggestions
- Cancel rate (high cancel is normal while typing)
- Accept rate vs latency (slow suggestions get rejected even when correct)

## Lab: prove code-as-draft helps

1. Serve a 7B rewrite model with vLLM.
2. Baseline: normal generation of editable region.
3. Implement naive speculation: chunk input region into 16–32 token blocks; ask model to continue with forced alignment until mismatch (or use engine speculative APIs if available).
4. Compare tokens/sec and TTFT on 200 recorded prompts.
5. Plot speedup vs fraction of region that stays unchanged (should correlate).

## Lab: cache sensitivity

1. Fix related excerpts; vary only the last 40 chars near cursor.
2. Measure KV cache hit rate with different packing orders.
3. Adopt the packing order with best p95 under typing traces.

## Connection to quality

Faster models can be smaller. Smaller models need better distillation. Better distillation needs better teachers and filters.

Latency work is not optional polish — it changes which model sizes are viable, which changes the whole training plan.

## What “done” looks like for this chapter

You can explain code-as-draft speculation on a whiteboard, list your latency budget, and know which Zed client behaviors you’ll verify before blaming model quality.
