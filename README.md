# 👗 3-Photo Style Analysis — an agentic workflow

A design for a **casual, for-fun** style analysis: give it **3 photos of the same
person**, and a small team of specialized AI "personas" reads proportions, body
shape, and coloring, then hands back a short, warm breakdown — *"try these styles,
lean into these principles, and here's how to tweak that trend so it actually fits
you."*

It is **food for thought, not a verdict.** No "flaws," no rules you must obey — just
grounded ideas to play with. Your eye is always the real authority.

---

## What's in this repo

| Path | What it is |
|------|-----------|
| [`docs/01-research-synthesis.md`](docs/01-research-synthesis.md) | The grounding: synthesized research on proportion assessment, body-shape systems, styling principles, and color analysis — with a numbered **principle library** every recommendation traces back to. |
| [`docs/02-workflow-architecture.md`](docs/02-workflow-architecture.md) | The pipeline: agent roster, data-flow graph, orchestration contract, and guardrails. |
| [`docs/04-output-template.md`](docs/04-output-template.md) | The final breakdown format the user actually reads, with a worked example. |
| [`personas/`](personas) | One ready-to-use brief per agent (0–8): role, method, output contract, guardrails. |
| [`schemas/`](schemas) | Structured handoff contracts between agents ([`.md`](schemas/handoffs.md) + [`.json`](schemas/handoffs.json)). |

## How it works, in one picture

```
3 photos ─▶ Intake ─▶ Proportion ┐
                   └▶ Color       ├▶ Shape ─▶ Strategy ─▶ Curator  ┐
                                  │                     └▶ Trend Skeptic ├▶ Editor ─▶ 📋 Breakdown
                                  └──────────── color joins ──────────────┘
```

Nine personas, each with one job:

0. **Orchestrator** — runs the pipeline, enforces schemas, degrades gracefully.
1. **Intake & Image-Quality Analyst** — what can these photos actually support?
2. **Proportion Analyst** — vertical/horizontal proportions in head-height units.
3. **Color & Contrast Analyst** — undertone & contrast lean (heavily caveated).
4. **Body-Shape Classifier** — a shared vocabulary, held loosely.
5. **Silhouette & Line Strategist** — turns proportions into *styling goals* + *why*.
6. **Style Curator** — goals → concrete cuts, rises, necklines, outfits to try.
7. **Trend Skeptic / Fit Critic** — "don't copy that trend blindly — here's the tweak."
8. **Synthesizer / Editor-in-Chief** — the short, warm, prioritized final read.

## Design principles

- **Describe, then prescribe.** Analysis agents state facts; strategy agents make
  choices; the editor curates. Separating the two is what keeps advice specific
  instead of generic.
- **Everything is grounded.** Each recommendation cites a principle ID from the
  research synthesis — no vibes-only styling.
- **Confidence is honest.** A handful of photos ≠ a tailor's tape. Every claim carries
  a confidence level; low-confidence guesses get hedged or dropped, never laundered
  into facts. Color especially is "a hunch to test in daylight."
- **Body-neutral and inclusive.** Works across genders and body types; no "problem
  areas," no flattery-as-obligation. Stated preferences always beat the rulebook.

## How to run it (conceptually)

The personas and schemas are model-agnostic prompt units. To execute:
feed the 3 photos + optional context to the Orchestrator brief, which invokes each
persona in the order above, validating each structured handoff against `schemas/`,
and passes the collected outputs to the Synthesizer for the final breakdown.

> ⚠️ **Scope & disclaimer.** This is entertainment, not professional image
> consulting, medical, or fitness advice. It reasons about *clothes and proportion*,
> never about a person's worth, weight, or health.
