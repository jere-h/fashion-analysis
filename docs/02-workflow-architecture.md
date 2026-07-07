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

### Runtime note — who sees the images (and is that enough?)
Only the **Analyst** needs vision. When photos are **pasted into a chat**, they're
visible only to the **orchestrating (main) agent's context**, not to freshly spawned
subagents (which receive text prompts). When photos exist as **files on disk**, any
subagent can see them via `Read`. This split matters, so the runner branches:

- **Images as files → distribute vision.** Run the **Analyst as a subagent** on the file
  paths; the Editor may also re-`Read` them for a final check. No bottleneck.
- **Images pasted only → the orchestrator is the sole set of eyes.** This is the weaker
  case, so it's hardened rather than left naive (below).

**Is single-vision effective?** The four personas are distinct *reasoning* modes
(describe → prescribe → critique → present), and there is only ever **one** set of photos
— so a single careful perceiver is not itself the problem. The real risks are (a) an
unchecked visual estimate propagating downstream, and (b) text-only agents losing nuance
the JSON didn't carry. Three mechanisms address both:

1. **Verified single pass.** The Analyst measures, then runs a second pass that actively
   tries to *disagree* with the first (re-anchor the head unit, re-check the leg break,
   cross-method torso:leg and shoulder:hip). Conflict lowers confidence instead of being
   averaged away — an adversarial check where independent agents aren't possible.
2. **Rich serialization.** The `StyleProfile` carries a free-text `visual_notes` field so
   the Stylist and Skeptic get grounding (posture, features to feature, current outfit
   read), not just ratios.
3. **Final visual reality-check.** Because the orchestrator still holds the images while
   composing the Editor step, it re-looks at the photos and confirms the recommendations
   don't contradict what's plainly visible — the one agent with eyes validates the end
   result. This turns the constraint into a closing safeguard.

The **Stylist**, **Skeptic**, and **Editor** remain text-only and run as independent
subagents (worth it for the Skeptic especially, so its critique isn't anchored to the
Stylist's reasoning). Each reads its brief + the principle library and the upstream JSON.

### Scope & sanity (pre-flight, enforced by the Analyst)
- **Same-person check** — confirm the 3 photos show the same person; else ask.
- **Usable photos** — need ≥1 full-body frame; if not, ask for a better shot instead of
  guessing.
- **Ephemeral** — treat images as for-the-moment analysis, not stored data.
- **Not professional advice** — entertainment only; never medical, fitness, or
  weight-related.

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
