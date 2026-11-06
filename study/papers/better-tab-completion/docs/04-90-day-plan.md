# 04 — 90-Day Execution Plan

Assumption: you can code Rust + Python, have one GPU (or rented), and will dogfood daily in Zed.

## North-star metric

Pick one and don’t move the goalposts weekly:

> **Accept rate ↑ while suggestions/minute ↓**, at **p95 TTFT ≤ 300ms** on your machine, vs Copilot + stock Zeta on the same week of work.

Keep a simple spreadsheet: date, provider, accepts, shows, keystrokes, notes.

---

## Phase 0 — Days 1–7: Bootstrap

### Lab 0.1 — Stock baselines
- Use Zed with Zeta for 2 days; journal failures.
- Switch to Copilot for 2 days; journal failures.
- Tag each miss: `wrong_edit` | `reversed_me` | `too_slow` | `too_noisy` | `missed_jump` | `needed_other_file`.

### Lab 0.2 — Clone and map
```bash
git clone https://github.com/zed-industries/zed.git
cd zed
# follow upstream docs to build for your OS
```
Read `docs/01-zed-fork-map.md` and open the listed files. Draw the lifecycle once from memory.

### Lab 0.3 — Local provider wire-up
Run any OpenAI-compatible completions server (vLLM/llama.cpp/Ollama). Point Zed:

```json
{
  "edit_predictions": {
    "provider": "open_ai_compatible_api",
    "open_ai_compatible_api": {
      "api_url": "http://127.0.0.1:8000/v1/completions",
      "model": "your-model",
      "prompt_format": "zeta2",
      "max_output_tokens": 512
    }
  }
}
```

Log raw prompts for 50 keystrokes. Annotate them.

**Exit criteria:** you can explain every section of a real Zed edit-prediction prompt.

---

## Phase 1 — Days 8–30: Teacher + Context (no student yet)

### Lab 1.1 — Teacher prompt
Start from Zed’s `teacher.md`. Adapt for your teacher API (chat vs completion). Add your failure tags from Phase 0 as few-shot negative/positive cases.

### Lab 1.2 — Frozen eval (n=50)
Hand-build 50 states with expected outcomes (`NO_EDITS`, continuation, refactor finish, reversal traps). **Freeze.** All future changes must report scores on this set.

### Lab 1.3 — Context packer offline
Write a Python tool that, given `(file, cursor, history, diagnostics)`, emits a Zeta-like prompt. Ablate:
- history on/off
- diagnostics on/off
- related files on/off

### Lab 1.4 — Teacher-in-the-loop dogfood
Serve teacher predictions through Zed (accept the latency). Measure accept rate vs Zeta/Copilot for one week of *your* coding.

**Exit criteria:** teacher+good context beats Copilot for you on accept rate *or* clearly loses only on latency (then proceed). If it loses on quality, do not train a student yet — fix prompts/context.

---

## Phase 2 — Days 31–60: Data + Student

### Lab 2.1 — Dataset v0 (1k–10k)
Follow `docs/02-dataset-design.md`. Mix:
- your traces
- commit-derived mid-states
- teacher labels
- heavy `NO_EDITS` + reversal filters

### Lab 2.2 — Train student
Base: modern 3B–8B code model (Zeta lineage used Qwen2.5-Coder then Seed-Coder — pick a current open coder).
- LoRA SFT first
- Format exactly what Zed will send (`zeta2` / `zeta2_1`)

### Lab 2.3 — Offline eval
Report: format validity, reversal rate, delta vs teacher (multi-ref if possible), frozen-eval pass rate.

### Lab 2.4 — Replace teacher in Zed
Serve student with vLLM; dogfood. Keep teacher as shadow judge offline.

**Exit criteria:** student within striking distance of teacher on frozen eval; latency feelably better than teacher.

---

## Phase 3 — Days 61–90: Policy, Speed, Fork Leverage

### Lab 3.1 — Silence policy
Add abstain:
- train `NO_EDITS` heavily, or
- DPO on (good suggest vs bad suggest), or
- simple accept-probability head

Target: fewer shows, higher accept rate (Cursor’s published direction).

### Lab 3.2 — Latency pass
Implement items from `docs/03-speculative-edit-decoding.md`:
- prefix cache packing order
- cancelation correctness
- speculative decode or shorter patch format experiment

### Lab 3.3 — Targeted Zed fork (only if blocked)
Pick **one**:
- richer related-excerpt retrieval
- jump UX polish
- extra context channel (e.g. last accepted prediction marker fidelity)
- better local provider streaming

Land the smallest patch that moves your metric.

### Lab 3.4 — Hardening
- secret redaction
- max region size
- language stratification
- regression suite in CI for prompt parse + reversal filter

**Exit criteria:** you beat your Phase-0 Copilot/Zeta personal baseline on the north-star metric for 5 consecutive workdays.

---

## What not to do in 90 days

- Training a 70B from scratch
- Building a new editor UI framework
- Betting on a Wasm extension for Tab
- Endless base-model shopping without a frozen eval
- Adding agents/chat features (different product)

---

## After day 90

If you won on personal dogfood:

1. Multi-language expand
2. Online preference loop
3. Consider upstreaming editor-side improvements
4. Cross-file jumps
5. Next-action prediction beyond edits (terminal, etc.) — only after edit Tab is clearly strong
