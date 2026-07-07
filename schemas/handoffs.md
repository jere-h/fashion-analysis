# Inter-Agent Handoff Schemas

Every persona emits **structured data only** — no prose crosses agent boundaries.
This keeps the Synthesizer working from facts, not from other agents' rhetoric, and
lets the Orchestrator validate and degrade gracefully. Below is each schema as a
compact JSON-Schema-style contract. A machine-readable copy is in
[`handoffs.json`](handoffs.json).

Shared convention — every leaf claim uses this envelope:

```jsonc
{ "value": <any>, "confidence": "high|medium|low", "basis": "which photo / cue" }
```

---

### IntakeReport  (Persona 1 → all)
```jsonc
{
  "photos": [
    { "id": "p1", "view": "front|side|back|three-quarter|upper-body|face",
      "framing": "full-body|cropped", "pose": "neutral|contrapposto|seated|angled",
      "usability": "good|fair|poor", "notes": "…" }
  ],
  "basis": { "unit": "head-height", "anchor_photo_id": "p1", "reliable": true },
  "coverage": {
    "proportions": "well-supported|partial|unavailable",
    "horizontal_balance": "well-supported|partial|unavailable",
    "posture": "well-supported|partial|unavailable",
    "color": "well-supported|partial|unavailable"
  },
  "limitations": ["baggy clothing obscures waist", "warm indoor cast"],
  "overall_confidence_ceiling": "high|medium|low"
}
```

### ProportionProfile  (Persona 2 → 4, 5, 8)
```jsonc
{
  "vertical": {
    "height_in_heads": { "value": 7.5, "confidence": "medium", "basis": "p1" },
    "torso_leg": { "value": { "ratio": "~1.05", "verdict": "long-torso|balanced|long-leg" },
                   "confidence": "medium", "basis": "p1+p2 agree" },
    "waist_placement": { "value": "high|balanced|low", "confidence": "…", "basis": "…" },
    "rise_read": { "value": "…", "confidence": "…", "basis": "…" },
    "neck": { "value": "short|average|long", "confidence": "…", "basis": "…" },
    "limb_length": { "value": "…", "confidence": "…", "basis": "…" }
  },
  "horizontal": {
    "shoulder_waist_hip": {
      "value": { "relative": "shoulders≈hips, soft waist", "widest_point": "hips",
                 "narrowest_point": "waist" },
      "confidence": "…", "basis": "p1" }
  },
  "caveats": ["…"]
}
```

### ColorProfile  (Persona 3 → 6, 8)
```jsonc
{
  "undertone": { "value": "warm|cool|neutral", "confidence": "low", "basis": "p3 face" },
  "contrast":  { "value": "high|medium|low", "confidence": "…", "basis": "…" },
  "season_direction": { "value": "e.g. Winter-leaning", "confidence": "low" },
  "wearable_notes": ["neutrals: charcoal, true white", "lean into contrast", "test: jewel tones"],
  "lighting_caveat": "Read from uncontrolled light — confirm in neutral daylight."
}
```

### ShapeProfile  (Persona 4 → 5, 8)
```jsonc
{
  "primary":   { "value": "rectangle", "confidence": "medium" },
  "secondary": { "value": "soft-hourglass", "confidence": "low" },
  "defining_feature": "balanced shoulders/hips + minimal waist indent",
  "plain_description": "balanced shoulders and hips with a softly defined waist",
  "notes": ["a starting vocabulary, not a verdict — you may not fully agree"]
}
```

### StyleStrategy  (Persona 5 → 6, 7, 8)
```jsonc
{
  "goals": [
    { "goal": "define a waist", "principle_id": "P-BAL-02",
      "mechanism": "belt/tuck at natural waist", "leverage": "high",
      "rationale": "adds shape to a balanced frame" }
  ],
  "focal_point": { "where": "waist", "why": "highest-leverage change" },
  "line_strategy": { "vertical": "columns, V-necks", "horizontal": "one belt break at waist" },
  "preference_reconciliation": ["likes oversized → keep one axis fitted"]
}
```

### Recommendations  (Persona 6 → 8)
```jsonc
{
  "moves": [ { "goal_ref": "define a waist", "garment_move": "wrap dress",
               "why": "follows and marks the waist", "confidence": "medium" } ],
  "fit_map": { "upper": "semi-fitted", "waist": "defined", "lower": "relaxed" },
  "necklines": [ { "value": "V / scoop", "why": "lengthen neck, narrow torso" } ],
  "rises":     [ { "value": "high-rise", "why": "resets waist up, lengthens leg" } ],
  "hemlines":  [ { "value": "ankle-crop", "why": "hits a lengthening point" } ],
  "color_moves": { "neutrals": ["charcoal"], "hero_colors": ["emerald"],
                   "contrast_strategy": "bold blocking" },
  "archetypes": [ { "name": "relaxed tailoring", "when": "work", "why": "…" } ]
}
```

### TrendNotes  (Persona 7 → 8)
```jsonc
{
  "looks": [
    { "look": "head-to-toe oversized", "verdict": "cooperate|tweak|reconsider",
      "adjustment": "keep one axis fitted", "portable_rule": "volume needs an anchor",
      "confidence": "medium" }
  ],
  "myths_busted": [ { "myth": "petites can't wear maxi", "why": "works with a high break + vertical line" } ]
}
```

### RunManifest  (Persona 0 → 8)
```jsonc
{
  "ran": ["1","2","3","4","5","6","7"],
  "skipped": [ { "agent": "posture read", "reason": "no side photo" } ],
  "confidence_ceiling": "medium"
}
```

---

**Validation rules (enforced by Orchestrator):**
- Reject any output missing required top-level keys or with an invalid enum value.
- Any leaf without a `confidence` is invalid.
- Downstream confidence is capped at the min of the upstream fields it depends on.
- `unavailable` coverage → the dependent fields must be `null`/omitted, not guessed.
