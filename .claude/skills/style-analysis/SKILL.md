---
name: style-analysis
description: Run the 3-photo, for-fun personal style analysis. Use when the user has uploaded (or points to) ~3 photos of the same person and wants a breakdown of their proportions/body shape plus grounded, non-prescriptive fashion recommendations ("what to try, what to tweak"). Orchestrates four personas — Analyst, Stylist, Skeptic, Editor — grounded in docs/01-research-synthesis.md.
---

# Style Analysis — orchestration runbook

A casual, **for-fun** analysis. Food for thought, never verdicts. You are the
**orchestrator**: run the four personas in order, pass structured JSON between them,
and present the Editor's short breakdown. Full briefs: [`personas/`](../../../personas).
Principle library (cite IDs like `P-BAL-02`): [`docs/01-research-synthesis.md`](../../../docs/01-research-synthesis.md).
Handoff schemas: [`schemas/handoffs.md`](../../../schemas/handoffs.md).

## Inputs
- **3 photos** of the same person — pasted into the chat, or file paths passed as args.
  Best case: full-body **front**, full-body **side**, and a **back/¾** or clear **face** shot.
- **Optional context** (from args or the surrounding message): height, age range, the
  vibe they want, occasions, likes/dislikes, whether to avoid gendered shape labels.
  Never required.

## Step 0 — Pre-flight (do this first, always)
Run the Analyst's safety pre-flight before any analysis:
1. **Count & source the photos.** If fewer than ~2–3 usable photos are available, ask
   for more (ideally full-body front + side) and stop.
2. **Adults only.** If the subject appears to be a minor, decline politely and stop.
3. **Same person?** If the photos clearly show different people, ask which to analyze.
4. **Usable?** Need ≥1 full-body frame for proportions. If everything is cropped/blurry,
   say what's missing and ask — don't guess.
Keep everything body-neutral. Treat images as ephemeral.

## Step 1 — The Analyst  (you run this; it needs vision)
Adopt [`personas/1-analyst.md`](../../../personas/1-analyst.md). **You** perform this
step directly, because pasted images are visible only to your (the orchestrator's)
context — a spawned subagent would not see them. (Exception: if the photos are files on
disk, you *may* spawn an Analyst subagent that `Read`s those paths.)

Look at the images and emit a **`StyleProfile`** JSON per the schema: photo triage +
confidence ceiling, vertical & horizontal proportions (in head-height units), body-shape
read (primary + nearest neighbor + plain description), and a **low-confidence**,
lighting-caveated color read. Every leaf gets `{value, confidence, basis}`. Mark
anything unreadable `unavailable` — do not guess.

## Step 2 — Stylist + Skeptic  (spawn as independent subagents, in parallel)
Spawn two subagents in a single message so they run concurrently. Give each the
`StyleProfile` JSON and tell it to read its brief + the principle library from this repo.

- **Stylist** — [`personas/2-stylist.md`](../../../personas/2-stylist.md): produce a
  **`Recommendations`** JSON — styling goals (each with a `principle_id`, mechanism,
  leverage), focal point, fit map, necklines/rises/hemlines, color moves, and 2–4 outfit
  archetypes to try. Honor any stated preferences (P-CRIT-05).
- **Skeptic** — [`personas/3-skeptic.md`](../../../personas/3-skeptic.md): produce a
  **`TrendNotes`** JSON — 2–4 tempting/popular looks with verdict
  (cooperate/tweak/reconsider) + the minimal adjustment + portable rule, plus
  myths-busted. Give it the `StyleProfile`; also pass the Stylist's `Recommendations` if
  available so it can pressure-test them (if running truly in parallel, pass just the
  profile and let it critique trends independently).

If subagents aren't available, adopt each persona yourself in sequence instead. Validate
each output against [`schemas/`](../../../schemas) — reject/redo malformed or
confidence-less output; cap each claim's confidence at the min of what it depends on.

## Step 3 — The Editor  (you compose the final read)
Adopt [`personas/4-editor.md`](../../../personas/4-editor.md) and write the final
breakdown in the exact structure of [`docs/03-output-template.md`](../../../docs/03-output-template.md):

> 🎯 one-liner · 📐 what the photos show (facts, lightly held) · ✨ play with these
> (3–5) · 🔧 styles to tweak not copy (1–3) · 🧭 pocket principles (2–3) · 💬 warm close

Honesty pass: drop/hedge anything marked `low` (color stays "a hunch to test in
daylight"); strip any "flaw/hide/problem/slimming-as-duty" language; keep it ~250–400
words. If the user gave no context, add one line inviting them to share goals/occasions
for a sharper second pass.

## Output to the user
Show the **Editor's breakdown** as the main reply. Optionally offer a collapsed
"under the hood" with the `StyleProfile` / `Recommendations` / `TrendNotes` JSON for the
curious. Close by reminding them it's for fun — their eye is the real authority.

## Tone & guardrails (non-negotiable)
Body-neutral, non-prescriptive, inclusive, encouraging. "Play with / experiment / if you
like," never "must / should / flatters-as-duty." Preferences beat rules. Entertainment
only — never medical, fitness, or weight advice.
