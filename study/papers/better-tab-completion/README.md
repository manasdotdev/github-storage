# SOTA Tab Completion for Zed — Study Pack

Curriculum for building Cursor-class (or better) **edit prediction** in Zed.

This is not “autocomplete.” Cursor Tab and Zed Zeta predict **your next edit** (and often **where to jump next**), then let you accept with Tab. Extensions cannot currently deliver that UX. You need Zed core + a strong model loop.

## Paths (pick one)

| Path | Match Cursor UX? | What you ship |
|------|------------------|---------------|
| **A. Provider-first** | Often “good enough” fast | Better model + context via Zed’s existing `open_ai_compatible_api` / Ollama providers |
| **B. Zed fork / upstream** | Yes | Context, jumps, UI, telemetry, custom providers |
| **C. Wasm extension only** | No | LSP menu completions at best |

**Recommended sequence:** A → prove quality → B for anything the provider API cannot express.

## Docs in this pack

1. [`docs/01-zed-fork-map.md`](docs/01-zed-fork-map.md) — real crates, request lifecycle, where to patch
2. [`docs/02-dataset-design.md`](docs/02-dataset-design.md) — traces, distillation, reversal problem, evals
3. [`docs/03-speculative-edit-decoding.md`](docs/03-speculative-edit-decoding.md) — why Tab feels instant
4. [`docs/04-90-day-plan.md`](docs/04-90-day-plan.md) — week-by-week labs
5. [`labs/CHECKLIST.md`](labs/CHECKLIST.md) — track progress

## Mental model (memorize this)

```
keystroke/cursor move
    → debounce / cancel in-flight
    → pack context (excerpt, edit history, related files, LSP defs, diagnostics)
    → model predicts rewrite of editable region (+ optional next cursor)
    → parse → diff against buffer
    → policy: show or silence (NO_EDITS)
    → ghost UI
    → Tab accept / reject / partial accept / jump
    → telemetry → distill / DPO / online RL
```

## High-signal reading (external)

- Zed: [Edit Prediction launch](https://zed.dev/blog/edit-prediction), [Zeta2](https://zed.dev/blog/zeta2), [How we developed Zeta2](https://zed.dev/blog/how-we-developed-zeta2)
- Zed docs: [Edit Prediction providers](https://zed.dev/docs/ai/edit-prediction)
- Cursor: [Tab model update](https://cursor.com/blog/tab-update), [Tab RL](https://cursor.com/blog/tab-rl)
- Code: [zed-industries/zed](https://github.com/zed-industries/zed) — start at `crates/edit_prediction/`
- Model: [zed-industries/zeta](https://huggingface.co/zed-industries) on Hugging Face
- Teacher prompt (gold): `crates/edit_prediction_cli/src/prompts/teacher.md`

## Week-0 bootstrap (do this today)

```bash
# 1) Clone Zed (shallow is fine to start)
git clone --depth 1 https://github.com/zed-industries/zed.git
cd zed

# 2) Open these files first (order matters)
# crates/edit_prediction/src/edit_prediction.rs   # EditPredictionStore orchestration
# crates/edit_prediction/src/zeta.rs              # Zeta provider + excerpt
# crates/edit_prediction/src/cursor_excerpt.rs    # What window goes to the model
# crates/edit_prediction/src/prediction.rs        # Prediction object / diff apply
# crates/zeta_prompt/src/zeta_prompt.rs           # Prompt input structs + formatting
# crates/edit_prediction_cli/src/prompts/teacher.md

# 3) Point stock Zed at a local OpenAI-compatible server (no fork required yet)
# settings.json → edit_predictions.provider = open_ai_compatible_api
```

See Lab 0 in [`docs/04-90-day-plan.md`](docs/04-90-day-plan.md).

## License note

Zed is GPL-3.0-or-later. Forks that distribute binaries must comply. Prefer upstream PRs when you can.
