# Agentic Workflow Architecture

> How the "3-photo style analysis" runs end to end: the agents, what each one
> is responsible for, how data flows between them, and the guardrails that keep
> the output grounded, useful, and kind.

This is a **casual, for-fun** analysis. The whole pipeline is designed to give
*food for thought* — "try this, experiment with that, here's why" — not verdicts.
Every stage carries confidence levels and caveats, because a handful of photos is
not a tailor's measuring tape.

---

## 1. Inputs

| Input | Required | Notes |
|-------|----------|-------|
| **3 photos of the same person** | Yes | Best case: (1) full-body **front**, arms slightly away from body, fitted or neutral clothing; (2) full-body **side profile**; (3) a **third angle** — back or 3/4 turn — *or* a clearer upper-body/face shot for color. |
| Self-reported context | Optional | Height, age range, the vibe they're going for, occasions to dress for, things they like/dislike, any "no-go" zones. Improves relevance; never required. |
| Consent + tone preference | Implicit | Analysis stays body-neutral and non-judgmental by default. |

**Why 3 photos?** One photo flattens a 3-D body into a single projection and hides
depth, posture, and true proportions. Three angles let the pipeline cross-check
vertical proportions (front + side), depth/posture (side), and horizontal balance
(front + back), and let the color stage find at least one frame with usable light.

---

## 2. Agent roster (personas)

Each persona is a specialized agent with a single job, a grounding reference, and a
strict output contract. Full persona briefs live in [`/personas`](../personas).

| # | Persona | One-line job | Depends on |
|---|---------|--------------|------------|
| 0 | **Orchestrator** | Runs the pipeline, enforces schemas, merges confidence, handles missing data | — |
| 1 | **Intake & Image-Quality Analyst** | Validates the 3 photos, labels what each shows, sets a measurement basis (head-height units), flags limitations | inputs |
| 2 | **Proportion Analyst** | Vertical & horizontal proportions from visual cues; waist placement, torso:leg, shoulder:waist:hip balance, limb length | 1 |
| 3 | **Color & Contrast Analyst** | Undertone lean, value-contrast level, rough seasonal direction — heavily caveated for lighting | 1 |
| 4 | **Body-Shape Classifier** | Maps proportions to a recognized shape vocabulary, descriptively, with confidence | 2 |
| 5 | **Silhouette & Line Strategist** | Turns proportions + shape into *styling goals* using balance/line principles (the "why" engine) | 2, 4 |
| 6 | **Style Curator** | Translates goals into concrete garment moves: necklines, rise, hemlines, fit maps, outfit archetypes to try | 5, 3 |
| 7 | **Trend Skeptic / Fit Critic** | The "don't blindly follow that trend — here's the adjustment" voice; pressure-tests popular styles against *this* person | 5, 6 |
| 8 | **Synthesizer / Editor-in-Chief** | Compresses everything into the short, warm, prioritized breakdown; owns tone | all |

**Design principle:** analysis personas (1–4) *describe*, strategy personas (5–7)
*prescribe*, and the editor (8) *curates*. Keeping "what is true" separate from
"what to do" is what stops the output from becoming a pile of generic rules.

---

## 3. Data flow

```
                        ┌─────────────────────────────┐
        3 photos  ─────▶│ 1. Intake & Image Quality   │
        (+context)      │    → photo map, basis unit, │
                        │      confidence, caveats     │
                        └───────────────┬─────────────┘
                                        │  IntakeReport
                        ┌───────────────┴───────────────┐
                        ▼                                ▼
              ┌───────────────────┐            ┌───────────────────┐
              │ 2. Proportion     │            │ 3. Color &        │
              │    Analyst        │            │    Contrast       │
              └─────────┬─────────┘            └─────────┬─────────┘
                        │ ProportionProfile              │ ColorProfile
                        ▼                                │
              ┌───────────────────┐                      │
              │ 4. Body-Shape     │                      │
              │    Classifier     │                      │
              └─────────┬─────────┘                      │
                        │ ShapeProfile                   │
                        ▼                                │
              ┌───────────────────┐                      │
              │ 5. Silhouette &   │                      │
              │    Line Strategist│                      │
              └─────────┬─────────┘                      │
                        │ StyleStrategy                  │
              ┌─────────┴──────────┐                     │
              ▼                    ▼                      │
    ┌──────────────────┐  ┌──────────────────┐           │
    │ 6. Style Curator │  │ 7. Trend Skeptic │           │
    └─────────┬────────┘  └────────┬─────────┘           │
              │ Recommendations     │ TrendNotes          │
              └──────────┬──────────┴─────────────────────┘
                         ▼
              ┌────────────────────────┐
              │ 8. Synthesizer /       │
              │    Editor-in-Chief     │  ──▶  Final Breakdown
              └────────────────────────┘
```

Stages 2 & 3 run in parallel (both need only Intake). Stages 6 & 7 run in parallel
(both need the Strategy). Everything else is sequential because it genuinely depends
on upstream output. The Color Profile joins late — it flavors recommendations but
never blocks the proportion → shape → strategy spine.

---

## 4. Orchestration contract

- **Structured handoffs.** Every persona emits a JSON object matching a schema in
  [`/schemas`](../schemas). No prose passes between agents — only structured data —
  so the Synthesizer works from facts, not from other agents' rhetoric.
- **Confidence is mandatory.** Each numeric or categorical claim carries a
  `confidence` in `{high, medium, low}` and a short `basis` (which photo, which cue).
  Low-confidence claims are allowed to flow through but the Editor must soften or
  drop them.
- **Graceful degradation.** If Intake flags that (say) no side photo exists, the
  Proportion Analyst marks depth/posture fields `unavailable` and the pipeline
  continues with reduced scope rather than failing.
- **No fabrication rule.** Agents may say "cannot determine from these photos."
  That is a valid, encouraged output. Guessing is penalized in the persona briefs.
- **Grounding reference.** Strategy personas must cite a principle ID from
  [`01-research-synthesis.md`](01-research-synthesis.md) for each recommendation,
  so "wear a higher rise" is always traceable to *why*.

---

## 5. Guardrails & tone

Baked into every persona brief and re-checked by the Editor:

1. **Body-neutral, never body-negative.** Describe proportions as neutral facts and
   frame all styling as *options that create a look you might enjoy*, not fixes for
   flaws. No language about "hiding," "flattering-as-obligation," weight, or health.
2. **Descriptive, not deterministic.** Body-shape labels are a shared vocabulary,
   not a cage. Always: "reads closest to X" with room to disagree.
3. **Preferences beat rules.** If the person says they love a look the "rules" would
   discourage, the correct output is *how to make that look work*, not "don't."
4. **Caveat the camera.** Lighting, lens, pose, and clothing distort everything —
   color most of all. Say so.
5. **Inclusive by construction.** Shape/proportion frameworks apply across genders
   and body types; the pipeline uses the framework that fits the person, and offers
   to skip gendered vocabulary entirely.
6. **It's for fun.** The closing tone is encouraging and experimental: "play with
   these," not "you must."

See [`04-output-template.md`](04-output-template.md) for the final format and
[`03-persona-library.md`](03-persona-library.md) for how each brief encodes these.
