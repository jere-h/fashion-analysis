# Agentic Workflow Architecture

> How the "3-photo style analysis" runs end to end: four agents, what each one
> owns, how data flows between them, and the guardrails that keep the output grounded,
> useful, and kind.

This is a **casual, for-fun** analysis — *food for thought*, not verdicts. Every stage
carries confidence levels and caveats, because a handful of photos is not a tailor's
measuring tape.

---

## 1. Inputs

| Input | Required | Notes |
|-------|----------|-------|
| **3 photos of the same person** | Yes | Best case: (1) full-body **front**, arms slightly away from body, fitted or neutral clothing; (2) full-body **side profile**; (3) a **third angle** — back or ¾ turn — *or* a clearer face shot for color. |
| Self-reported context | Optional | Height, age range, the vibe they want, occasions, likes/dislikes, any "no-go" zones. Improves relevance; never required. |

**Why 3 photos?** One photo flattens a 3-D body into a single projection and hides
depth, posture, and true proportion. Three angles let the pipeline cross-check vertical
proportions (front + side), depth/posture (side), horizontal balance (front + back),
and find at least one frame with usable light for color.

---

## 2. The four agents

Deliberately small. Each persona is one clear **mode of work** — read, recommend,
critique, present — not a sliver of a task. Full briefs live in
[`/personas`](../personas).

| # | Persona | Mode | One-line job |
|---|---------|------|--------------|
| 1 | **The Analyst** | *Describe* | Reads the 3 photos → proportions, body-shape read, and directional color, all confidence-tagged. |
| 2 | **The Stylist** | *Recommend* | Turns the facts into styling goals **and** concrete moves (rise, necklines, hemlines, fit map, outfit archetypes), each citing a principle. |
| 3 | **The Skeptic** | *Critique* | "Don't blindly follow that trend — here's the tweak," plus busts the tired rules. |
| 4 | **The Editor** | *Present* | Compresses everything into the short, warm, prioritized breakdown the reader sees. |

**Why these four (and not nine):** the only split that earns its keep is
**describe → prescribe → critique → present.** Keeping "what is true" (Analyst) separate
from "what to do" (Stylist) is what stops the advice from becoming a pile of generic
rules; the Skeptic is a genuinely different *adversarial* mode; the Editor owns voice.
Everything else — image-quality triage, proportion math, shape labels, color — is just
the Analyst's job, and orchestration is plain pipeline logic, not a character.

---

## 3. Data flow

```
                     ┌───────────────────────────────┐
   3 photos ────────▶│ 1. THE ANALYST                │
   (+ context)       │    photo triage · proportions │
                     │    · shape · color (directional)
                     └───────────────┬───────────────┘
                                     │ StyleProfile
                        ┌────────────┴────────────┐
                        ▼                         ▼
              ┌───────────────────┐     ┌───────────────────┐
              │ 2. THE STYLIST    │     │ 3. THE SKEPTIC    │
              │   goals + moves   │     │   trend tweaks    │
              └─────────┬─────────┘     └─────────┬─────────┘
                        │ Recommendations         │ TrendNotes
                        │   (Skeptic also reads ───┘
                        │    Recommendations)
                        ▼
              ┌────────────────────────┐
              │ 4. THE EDITOR          │ ──▶  📋 Final Breakdown
              └────────────────────────┘
```

- The **Analyst** runs first; everyone depends on its `StyleProfile`.
- The **Stylist** and **Skeptic** run next; the Skeptic reads the Stylist's
  `Recommendations` too (so it can pressure-test them), so in practice Stylist → Skeptic
  is a short sequence, or they run together with the Skeptic given the Stylist's output.
- The **Editor** runs last, over all three outputs.

A pipeline this short needs no separate orchestrator persona — the runner just invokes
the four in order, validating each handoff against [`/schemas`](../schemas).

---

## 4. Orchestration contract

- **Structured handoffs.** Each persona emits a JSON object matching a schema in
  [`/schemas`](../schemas). No prose crosses agent boundaries — the Editor works from
  facts, not from other agents' rhetoric.
- **Confidence is mandatory.** Every claim carries `confidence ∈ {high, medium, low}`
  and a short `basis`. Downstream confidence is capped at the min of what it depends on.
- **Graceful degradation.** If the Analyst marks a field `unavailable` (e.g., no side
  photo → no posture read), the pipeline continues with reduced scope rather than
  failing.
- **No fabrication.** "Can't determine from these photos" is a valid, encouraged output.
- **Grounding reference.** The Stylist and Skeptic must cite a principle ID from
  [`01-research-synthesis.md`](01-research-synthesis.md) for each recommendation, so
  "wear a higher rise" is always traceable to *why*.

---

## 5. Guardrails & tone

Baked into every persona brief and re-checked by the Editor:

1. **Body-neutral, never body-negative.** Proportions are neutral facts; styling is
   *options that create a look you might enjoy*, not fixes for flaws. No "hiding,"
   flattery-as-obligation, weight, or health language.
2. **Descriptive, not deterministic.** Shape labels are shared vocabulary, not a cage —
   always "reads closest to X," with a label-free description alongside.
3. **Preferences beat rules** (P-CRIT-05). Love a look the rules discourage? The answer
   is *how to make it work*, not "don't."
4. **Caveat the camera.** Lighting, lens, pose, and clothing distort everything — color
   most of all. Say so.
5. **Inclusive by construction.** Shape/proportion logic applies across genders and body
   types; use the framework that fits the person, and offer to drop gendered vocabulary.
6. **It's for fun.** The closing tone is encouraging and experimental.

See [`03-output-template.md`](03-output-template.md) for the final format and
[`01-research-synthesis.md`](01-research-synthesis.md) for the principle library.
