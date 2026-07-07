# 👗 3-Photo Style Analysis — an agentic workflow

A **casual, for-fun** style analysis you run inside Claude Code: give it **3 photos of
the same person**, and a small team of specialized AI "personas" reads proportions, body
shape, and coloring, then hands back a short, warm breakdown — *"try these styles,
lean into these principles, and here's how to tweak that trend so it actually fits
you."*

It ships as a **runnable skill** plus the persona briefs, schemas, and research that
ground it — not a standalone program. The "runtime" is Claude itself interpreting the
skill; see [How to run it](#how-to-run-it).

It is **food for thought, not a verdict.** No "flaws," no rules you must obey — just
grounded ideas to play with. Your eye is always the real authority.

---

## What's in this repo

| Path | What it is |
|------|-----------|
| [`docs/01-research-synthesis.md`](docs/01-research-synthesis.md) | The grounding: synthesized research on proportion assessment, body-shape systems, styling principles, and color analysis — with a numbered **principle library** every recommendation traces back to. |
| [`docs/02-workflow-architecture.md`](docs/02-workflow-architecture.md) | The pipeline: the four agents, data-flow graph, orchestration contract, and guardrails. |
| [`docs/03-output-template.md`](docs/03-output-template.md) | The final breakdown format the user actually reads, with a worked example. |
| [`personas/`](personas) | One ready-to-use brief per agent (1–4): role, method, output contract, guardrails. |
| [`schemas/`](schemas) | Structured handoff contracts between agents ([`.md`](schemas/handoffs.md) + [`.json`](schemas/handoffs.json)). |
| [`.claude/skills/style-analysis/`](.claude/skills/style-analysis/SKILL.md) | **The runnable skill** — the orchestration runbook you actually invoke. |
| [`images/`](images/) | Local **drop zone** for your 3 photos — git-ignored, so pictures are never committed. |

## How it works, in one picture

```
3 photos ─▶ 1. Analyst ─▶ 2. Stylist ─┐
                        └▶ 3. Skeptic ─┴▶ 4. Editor ─▶ 📋 Breakdown
```

Four personas — one clear mode of work each: **read → recommend → critique → present.**

1. **The Analyst** — reads the 3 photos: proportions, body-shape read, and directional
   color, all confidence-tagged. (This one persona does all the *describing*.)
2. **The Stylist** — turns the facts into styling goals **and** concrete moves (rise,
   necklines, hemlines, fit map, outfits to try), each citing a principle.
3. **The Skeptic** — "don't blindly follow that trend — here's the tweak," plus busts
   the tired rules.
4. **The Editor** — the short, warm, prioritized final read.

Orchestration is plain pipeline logic (invoke the four in order, validate each
handoff) — not a separate agent.

## Design principles

- **Describe, then prescribe.** The Analyst states facts; the Stylist makes choices;
  the Editor curates. Separating the two is what keeps advice specific instead of
  generic.
- **Everything is grounded.** Each recommendation cites a principle ID from the
  research synthesis — no vibes-only styling.
- **Confidence is honest.** A handful of photos ≠ a tailor's tape. Every claim carries
  a confidence level; low-confidence guesses get hedged or dropped, never laundered
  into facts. Color especially is "a hunch to test in daylight."
- **Body-neutral and inclusive.** Works across genders and body types; no "problem
  areas," no flattery-as-obligation. Stated preferences always beat the rulebook.

## How to run it

This runs **inside Claude Code** (CLI or IDE) — that's the runtime that reads the skill
and persona briefs. There's **no standalone CLI**; `git clone` alone won't "run" it.

1. Open this repo in Claude Code (installed + authenticated).
2. Provide **3 photos** of the same person (best: full-body front, side, and a back/¾ or
   clear face shot) — drop them in [`images/`](images/) (git-ignored) to use the stronger
   file-based vision path, or paste them into chat. Add optional context in your message —
   goals, occasions, likes, or "skip the gendered labels."
3. Invoke the **`style-analysis`** skill (e.g. `/style-analysis`), or just say
   *"run the style analysis on these."*
4. The orchestrator runs the pre-flight, does the Analyst read on your photos, spawns the
   Stylist + Skeptic, and returns the Editor's short breakdown.

**Under the hood** the personas and schemas are model-agnostic prompt units: the 3 photos
+ context go to the Analyst → `StyleProfile` → Stylist & Skeptic (independent) →
`Recommendations` + `TrendNotes` → Editor → the final breakdown. Each handoff is validated
against `schemas/`. See [`.claude/skills/style-analysis/SKILL.md`](.claude/skills/style-analysis/SKILL.md).

> Want a true `python run.py ./images` that hits the Claude API directly, with no Claude
> Code needed? That standalone runner isn't built yet — it can be added on request.

> 🔒 **Privacy — local-only images.** Your photos are handled **locally only**: never
> uploaded, transmitted to third-party services, stored remotely, embedded in artifacts,
> or committed to git (photo files and `images/` are `.gitignore`d as a backstop). Treat
> them as ephemeral. *Honest caveat:* running this through Claude Code / the Claude API
> sends the image to the Claude model endpoint for the vision step — the model seeing it,
> as with any model use; the workflow adds no further upload or storage. Zero transmission
> would require a fully local vision model.

> ⚠️ **Scope & disclaimer.** This is entertainment, not professional image
> consulting, medical, or fitness advice. It reasons about *clothes and proportion*,
> never about a person's worth, weight, or health.
