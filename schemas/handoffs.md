# Inter-Agent Handoff Schemas

Every persona emits **structured data only** — no prose crosses agent boundaries. This
keeps the Editor working from facts, not from other agents' rhetoric, and lets the
runner validate each step and degrade gracefully. Three handoffs, one per producing
agent (the Editor produces the final prose, not a schema). Machine-readable copy:
[`handoffs.json`](handoffs.json).

Shared convention — every leaf claim uses this envelope:

```jsonc
{ "value": <any>, "confidence": "high|medium|low", "basis": "which photo / cue" }
```

---

### `StyleProfile`  — The Analyst (1) → Stylist (2), Skeptic (3), Editor (4)

The Analyst folds photo-triage, proportions, body shape, and color into one object.

```jsonc
{
  "intake": {
    "photos": [
      { "id": "p1", "view": "front|side|back|three-quarter|face",
        "framing": "full-body|cropped", "pose": "neutral|angled|seated",
        "usability": "good|fair|poor" }
    ],
    "basis": { "unit": "head-height", "anchor_photo_id": "p1", "reliable": true },
    "coverage": {
      "proportions": "well-supported|partial|unavailable",
      "horizontal_balance": "well-supported|partial|unavailable",
      "posture": "well-supported|partial|unavailable",
      "color": "well-supported|partial|unavailable"
    },
    "limitations": ["baggy top obscures waist", "warm indoor cast"],
    "confidence_ceiling": "high|medium|low"
  },

  "proportions": {
    "vertical": {
      "height_in_heads":  { "value": 7.5, "confidence": "medium", "basis": "p1" },
      "torso_leg":        { "value": "long-torso|balanced|long-leg", "confidence": "…", "basis": "p1+p2" },
      "waist_placement":  { "value": "high|balanced|low", "confidence": "…", "basis": "…" },
      "neck":             { "value": "short|average|long", "confidence": "…", "basis": "…" },
      "limb_length":      { "value": "short|average|long", "confidence": "…", "basis": "…" }
    },
    "horizontal": {
      "shoulder_waist_hip": { "value": { "relative": "shoulders≈hips, soft waist",
                                         "widest_point": "hips", "narrowest_point": "waist" },
                              "confidence": "…", "basis": "p1" }
    }
  },

  "shape": {
    "primary":           { "value": "rectangle", "confidence": "medium" },
    "secondary":         { "value": "soft-hourglass", "confidence": "low" },
    "defining_feature":  "balanced shoulders/hips + minimal waist indent",
    "plain_description": "balanced shoulders and hips with a softly defined waist"
  },

  "color": {
    "undertone":       { "value": "warm|cool|neutral", "confidence": "low", "basis": "p3 face" },
    "contrast":        { "value": "high|medium|low", "confidence": "…", "basis": "…" },
    "season_direction":{ "value": "e.g. Winter-leaning", "confidence": "low" },
    "wearable_notes":  ["neutrals: charcoal, true white", "lean into contrast"],
    "lighting_caveat": "Read from uncontrolled light — confirm in neutral daylight."
  }
}
```

### `Recommendations`  — The Stylist (2) → Skeptic (3), Editor (4)

```jsonc
{
  "goals": [
    { "goal": "define a waist", "principle_id": "P-BAL-02",
      "mechanism": "belt / front-tuck at natural waist", "leverage": "high",
      "rationale": "adds shape to a balanced frame" }
  ],
  "focal_point": { "where": "waist", "why": "highest-leverage change" },
  "line_strategy": { "vertical": "columns, V-necks", "horizontal": "one belt break at waist" },
  "fit_map": { "upper": "semi-fitted", "waist": "defined", "lower": "relaxed" },
  "necklines": [ { "value": "V / scoop", "why": "lengthen neck, narrow torso", "principle_id": "P-LINE-03" } ],
  "rises":     [ { "value": "high-rise", "why": "resets waist up, lengthens leg", "principle_id": "P-FIT-01" } ],
  "hemlines":  [ { "value": "ankle-crop", "why": "hits a lengthening point", "principle_id": "P-LINE-02" } ],
  "color_moves": { "neutrals": ["charcoal"], "hero_colors": ["emerald"], "contrast_strategy": "bold blocking" },
  "archetypes": [ { "name": "relaxed tailoring", "when": "work", "why": "…" } ],
  "preference_reconciliation": ["likes oversized → keep one axis fitted (P-BAL-03)"]
}
```

### `TrendNotes`  — The Skeptic (3) → Editor (4)

```jsonc
{
  "looks": [
    { "look": "head-to-toe oversized", "verdict": "cooperate|tweak|reconsider",
      "adjustment": "keep one axis fitted", "portable_rule": "volume needs an anchor",
      "principle_id": "P-BAL-03", "confidence": "medium" }
  ],
  "myths_busted": [ { "myth": "petites can't wear maxi",
                      "why": "works with a high break + vertical line", "principle_id": "P-CRIT-03" } ]
}
```

---

**Validation rules (enforced by the runner):**
- Reject any output missing required top-level keys or with an invalid enum value.
- Any leaf without a `confidence` is invalid.
- Downstream confidence is capped at the min of the upstream fields it depends on.
- `unavailable` coverage → dependent fields must be `null`/omitted, not guessed.
- Every `Recommendations` / `TrendNotes` item must carry a `principle_id` that exists in
  [`../docs/01-research-synthesis.md`](../docs/01-research-synthesis.md) §7.
