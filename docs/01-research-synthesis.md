# Research Synthesis & Principle Library

Grounding for the whole workflow. This distills how professional stylists and image
consultants actually assess a person, the widely-accepted principles behind their
recommendations, **and** the well-earned skepticism about where those principles turn
into dated, one-size-fits-all "rules." Every recommendation the agents make cites a
**principle ID** (e.g., `P-BAL-02`) defined in [§7](#7-principle-library).

> Sourcing note: this synthesizes ~35 stylist, image-consultant, color-analysis, and
> body-shape sources plus two academic fashion-recommendation papers (see
> [§8](#8-sources)). It is popular/industry practice, not peer-reviewed science —
> appropriate for a for-fun tool. Body-shape frameworks in the wild skew heavily
> female; men's frameworks are the same balance logic with different labels, and we
> note where the evidence is thinner.

---

## 1. How professional consultants actually structure an assessment

Across training institutes (London Image Institute) and practitioner accounts
(Yellowbrick, The Honest Image, Style With Francesca), a consultation is a **sequenced
pipeline**, not a single judgment — which is exactly why an agentic workflow maps onto
it so cleanly:

1. **Intake / discovery** — lifestyle, work, occasions, goals, and *aspirational* (not
   just current) style come **first**, before any physical analysis. Recommendations
   downstream depend on this context.
2. **Color analysis** — undertone + hair + eye, ideally under controlled ~5000K light
   with fabric drapes, judging how skin *reacts* rather than by taste.
3. **Figure / body-line analysis** — body shape, **proportions**, and body type,
   assessed separately from body *size*.
4. **Style-personality analysis** — mapping to archetypes (Classic, Romantic,
   Expressive, etc.).
5. **Synthesis into recommendations** — silhouettes, cuts, fabrics, palette, wardrobe
   plan — generated *downstream from and dependent on* the earlier steps.

Two design-relevant findings: (a) **the mirror distorts self-assessment; photographs
give a more objective read** — direct support for a photo-based tool; (b) size and
shape are **distinct** and must be assessed separately.

→ Encoded as `P-PROC-01…03`.

---

## 2. Proportion assessment (the vertical & horizontal read)

Proportion has **two independent axes that must both be assessed** (Everyday Style,
Inside Out Style, MMNine):

### 2a. The head-unit ruler
The artistic standard (originating with Polykleitos, 5th c. BCE) measures the body in
**head-lengths** (crown to chin). The average person is ~**7.5 heads** tall; an
idealized figure ~8; extra length comes from chest and legs. Useful photo anchors:
legs (floor→crotch) ≈ 3.5–4 heads; arms ≈ 3 heads; the body's **vertical halfway
point** sits at the greater trochanters, just above the pubic arch. Because head-height
is *internal* to the photo, it cancels out unknown camera distance and real height.

→ `P-PROP-01`.

### 2b. Vertical proportion — torso vs legs
Divide the figure at the **leg break** (hip crease / crotch point). Compare
**head→leg-break** against **leg-break→floor**:
- upper longer → **long torso / shorter legs**
- lower longer → **long legs / shorter torso**
- roughly equal → **balanced**

Quantified as **"leg share" = inseam ÷ height**: long legs >52%, balanced 48–52%, long
torso <48% (MMNine). Waist *placement* (high/low) is a **separate** proportion from
torso length — you can have a long torso but a high waist. The **natural waist** is the
narrowest dip between rib cage and hip bone, *not* necessarily the belly button.

→ `P-PROP-02`, `P-PROP-03`.

### 2c. Horizontal proportion — shoulder : waist : hip balance
Read across the front frame using a **shoulder/bust-to-hip ratio**:
- ratio >1.05 → shoulders dominant (inverted-triangle read)
- ratio <0.95 → hips dominant (pear read)
- 0.95–1.05 → **balanced**

Waist definition is a **waist ÷ bust/hip ratio** (≤0.75 reads as a defined waist).
Men average a broader shoulder:hip ratio (~1:1.18) than women (~1:1.03), so the same
"balanced" verdict sits at a different numeric center by frame.

→ `P-PROP-04`, `P-PROP-05`.

**Photo-adaptation caveat:** consultants take these from a tape measure. From photos we
*estimate the same ratios visually* in head-units — so every proportion claim ships
with a confidence level and cross-checks front vs side (`P-PROP-06`).

---

## 3. Body-shape classification systems

Body shape is **relative proportion, not size** — an hourglass stays an hourglass
regardless of weight, because it's defined by the *relationship* among bust, waist,
hips, and shoulders (Sumissura, BodySpec, calculator.net, Omni). It's read via a
**decision tree keyed to waist definition**, then to which end is wider.

### 3a. Feminine-frame vocabulary (5–7 shapes)
Common thresholds (calculators converge on ~5% / ~3.6-inch bands):

| Shape | Defining rule (approx.) |
|-------|-------------------------|
| **Hourglass** | bust ≈ hips (within ~5% / ~1 in), waist ≥ ~25% smaller (waist÷hip ≤ 0.75) |
| **Soft hourglass** | same balance, gentler waist (waist÷hip 0.75–0.80) |
| **Rectangle / straight** | bust ≈ waist ≈ hips (all within ~5%); little waist definition. *Most common — ~46% of women* |
| **Pear / triangle** | hips ≥ ~5% wider than bust/shoulders, defined waist |
| **Inverted triangle** | shoulders/bust ≥ ~5% wider than hips |
| **Apple / round** | waist ≥ hips; trunk fuller than limbs |
| **Spoon / diamond** | pear- or apple-like variants (high-hip shelf / fuller mid) |

### 3b. Masculine-frame vocabulary
The same balance logic, relabeled: **trapezoid** (shoulders moderately > waist/hips —
often treated as the "balanced" masculine default), **inverted triangle** (shoulders ≫
waist, athletic V), **rectangle** (shoulder ≈ waist ≈ hip), **triangle** (hips/waist >
shoulders), **oval** (fuller midsection). Sourced material was thinner here, so the
classifier leans on the shoulder:waist:hip *ratios* (§2c) rather than trying to hit
exact male thresholds — flagged as medium/low confidence.

### 3c. Read this loosely — it's a starting vocabulary
Multiple stylists (and the calculators themselves) stress the labels are **outdated as
rules**: "somewhat outdated… best used to conceal or balance," culturally-driven
shifting binaries, over-generalized magazine categories that ignore real individual
variation. So we treat shape as **shared shorthand + nearest neighbor**, always paired
with a plain-language description, never a verdict.

→ `P-SHAPE-01…04`.

---

## 4. Core styling principles (the durable, widely-agreed core)

Stripped of trend and flattery-pressure, stylists converge on **three levers**:
**balance, definition, proportion** (The Well Dressed Life, QC Makeup Academy,
SwimRight, Bags & The Girl).

- **`P-BAL-01` Balance = distribute visual weight.** The common goal is a more even or
  *intentional* top-to-bottom read. Shapes already balanced (rectangle, hourglass)
  maintain it; others *create* it — **by choice, not obligation**.
- **`P-BAL-02` Definition / the waist is the master lever.** Introducing a waistline
  (belt, wrap, dart, front-tuck, high waistband, structured jacket) adds shape and
  makes legs read longer. Its *placement* redistributes the torso:leg ratio.
- **`P-BAL-03` Volume needs an anchor.** Loose ≠ hiding. Pair **one** voluminous piece
  with **one** fitted piece; two loose pieces together read out-of-proportion. "Loose
  to create a silhouette" works only when one axis stays defined.
- **`P-LINE-01` Vertical lines elongate.** Unbroken vertical lines — monochrome
  columns, vertical stripes, long open layers, center plackets — lengthen the
  silhouette by letting the eye travel uninterrupted.
- **`P-LINE-02` Where a line ends, the eye stops.** A hem/boot-shaft ending at the
  widest calf shortens the leg and widens the calf; continuing past it (maxi,
  knee-high) or ending at a slim point (ankle bone) reads longer.
- **`P-LINE-03` Necklines reshape the upper body.** V- and scoop-necks lengthen the
  neck/torso and draw the eye up; boat/high necks widen the shoulder line — choose per
  the focal point.
- **`P-PROP-07` Rule of thirds beats halves.** Splitting an outfit ~⅓ : ⅔ (e.g.,
  cropped jacket over high-rise full-length trousers) reads more dynamic; a 50/50 break
  reads "frumpy."
- **`P-FIT-01` Rise & hemline set the leg read.** High rise + hem at a lengthening
  point lengthens legs (remedy for long torso); mid rise avoids compressing a short
  torso. Placement matters more than the garment's label.
- **`P-EMPH-01` One focal point.** Concentrate emphasis in one place — where you want
  the eye to land — rather than three competing points.
- **`P-BAL-04` Shape-specific defaults** (apply *only* as starting hypotheses):
  - Pear → draw eye up, add upper-body interest/structured shoulders, streamline below.
  - Inverted triangle → add lower-body volume (A-line, wide-leg), keep a clean top,
    vertical necklines over shoulder details.
  - Rectangle → add definition (belts, wrap, tailored jackets) to create a waist.
  - Apple → vertical lines + structured shoulders to create length.
  - Hourglass → keep the waist visible; avoid boxy/drop-waist that erases it.

→ Directional worked examples for long/short torso in `P-FIT-02`.

---

## 5. Color analysis (directional only from photos)

Seasonal color analysis evaluates **three dimensions** (the concept wardrobe, Gabrielle
Arruda, Color Me Beautiful):

- **Hue / undertone** — warm (golden/yellow), cool (pink/blue), or neutral.
- **Value** — overall light-to-dark depth of hair + skin + eyes together.
- **Chroma** — whether saturated or muted colors harmonize.

These map to **4 base seasons** (Spring warm/light/clear; Summer cool/light/soft;
Autumn warm/deep/soft; Winter cool/deep/clear), often expanded to **12 sub-seasons**.
Two field shortcuts: **contrast level** (value gap across features — dark hair/light
skin = high contrast; uniform = low) is often *more actionable* than nailing the exact
season; and the **metal test** (gold flatters warm, silver flatters cool).

**Hard caveat baked into the pipeline:** pros drape fabric under controlled 5000K light
and judge skin *reaction*. Uncontrolled photos have color casts, filters, and mixed
light. So color output is **directional and low-confidence — "a hunch to test in
daylight," never a diagnosis.**

→ `P-COLOR-01…03`.

---

## 6. The critical counterweight (what to *not* blindly encode)

A strong strand of working stylists (Everyday Style, Fashion Journal, Urbanite|
Suburbanite, The Well Dressed Life) actively debunks the genre. This is the backbone of
the **Skeptic** persona and the tone guardrails:

- **`P-CRIT-01` Fit > rules.** How a garment actually fits and drapes on *your* body
  matters as much as any category rule; tailoring changes how a shape reads.
- **`P-CRIT-02` Highlight, don't hide.** Reframe from disguising "flaws" to featuring
  what you like. Body-shape labels describe visual-weight distribution — neutral info,
  not a ranking.
- **`P-CRIT-03` Debunked myths to avoid repeating:** oversized/loose clothes *don't*
  hide size (skimming fitted reads leaner); black isn't uniquely slimming (navy/charcoal
  equal; fit matters more); a smaller/tighter size makes you look *larger*, not smaller;
  length "rules" by age are not laws — balance and comfort decide.
- **`P-CRIT-04` Categories are rough guides, not identities.** The fruit taxonomy
  (Trinny & Susannah pear/apple) is over-generalized; treat all of this as
  experimentation to make a given item work, not fixed law.
- **`P-CRIT-05` Preferences win.** If someone loves a look the "rules" discourage, the
  job is to make it work for them, not to forbid it.

Academic footing (arXiv ViBE; Computational Fashion Recommendation survey) confirms
body shape is a real, under-modeled signal in recommendation — enough to justify a
proportion-aware tool, while staying honest that this is heuristic, not clinical.

---

## 7. Principle Library (canonical IDs)

The agents cite these. Each is a compressed, sourced rule of thumb.

| ID | Principle |
|----|-----------|
| **P-PROC-01** | Assess in sequence: context → color → figure/proportion → style-personality → synthesis. |
| **P-PROC-02** | Photos beat the mirror for objective silhouette reading. |
| **P-PROC-03** | Body *size* and body *shape* are distinct; assess separately, style the shape. |
| **P-PROP-01** | Measure in head-height units (crown→chin); ~7.5 heads average; cancels camera distance. |
| **P-PROP-02** | Vertical read = head→leg-break vs leg-break→floor → long-torso / balanced / long-leg. |
| **P-PROP-03** | Waist placement (high/low) is independent of torso length; natural waist = narrowest rib–hip dip. |
| **P-PROP-04** | Horizontal read = shoulder/bust ÷ hip ratio; >1.05 shoulder-dominant, <0.95 hip-dominant, else balanced. |
| **P-PROP-05** | Waist definition = waist ÷ bust/hip ≤ ~0.75 reads as a defined waist. |
| **P-PROP-06** | From photos these are *estimates* — attach confidence, cross-check front vs side. |
| **P-PROP-07** | Rule of thirds: split outfits ~⅓:⅔, not 50/50. |
| **P-SHAPE-01** | Shape = relative proportion, not size; read via waist-definition-first decision tree. |
| **P-SHAPE-02** | Feminine frame: hourglass / (soft) / rectangle / pear / inverted-triangle / apple / spoon-diamond, by ~5% bands. |
| **P-SHAPE-03** | Masculine frame: trapezoid / inverted-triangle / rectangle / triangle / oval, by the same ratios (thinner evidence → lower confidence). |
| **P-SHAPE-04** | Report primary + nearest neighbor + plain description; it's shorthand, not a verdict. |
| **P-BAL-01** | Balance = distribute visual weight top-to-bottom, by choice not obligation. |
| **P-BAL-02** | The waist is the master lever; defining it adds shape and lengthens the leg read. |
| **P-BAL-03** | Volume needs an anchor: pair one loose piece with one fitted; never two loose. |
| **P-BAL-04** | Shape-specific starting defaults (pear/inverted/rectangle/apple/hourglass) — hypotheses, not rules. |
| **P-LINE-01** | Vertical lines (monochrome, stripes, long layers, plackets) elongate. |
| **P-LINE-02** | A line ends where the eye stops; mind hem/boot termination vs the widest calf. |
| **P-LINE-03** | V/scoop lengthen & narrow up top; boat/high widen the shoulder line. |
| **P-FIT-01** | Rise + hemline placement drive the leg-length read more than garment labels. |
| **P-FIT-02** | Long torso → high rise, cropped jackets, tucks, wide belts. Short torso → mid rise, vertical necklines, longer blazers, monochrome. |
| **P-EMPH-01** | One focal point per outfit. |
| **P-COLOR-01** | Color has three dims: hue/undertone, value, chroma → 4 (→12) seasons. |
| **P-COLOR-02** | Contrast level & the metal test are the most photo-robust color signals. |
| **P-COLOR-03** | Uncontrolled-photo color is directional only — a daylight hunch, never a diagnosis. |
| **P-CRIT-01** | Fit and drape matter as much as category rules; tailoring changes the read. |
| **P-CRIT-02** | Highlight what you like; don't frame styling as hiding flaws. |
| **P-CRIT-03** | Debunk: loose≠slimming, black≠uniquely slimming, tighter-size≠smaller, age≠length-law. |
| **P-CRIT-04** | Shape categories are rough guides, not identities; experiment per item. |
| **P-CRIT-05** | The person's stated preferences override any rule. |

---

## 8. Sources

**Consultation structure / image consulting:** London Image Institute; Yellowbrick;
The Honest Image; Style With Francesca; Global Image Group; Stylist Srushtee.
**Proportion:** Inside Out Style Blog (×2); MMNine Lab; Everyday Style; Blufashion;
Wikipedia (Body proportions).
**Body-shape systems:** the concept wardrobe; calculator.net; Sumissura; Omni
Calculator; bodytypecalculator.org; BodySpec; QC Makeup Academy; JudyP Apparel;
Magnetic Modern Style.
**Styling principles:** The Well Dressed Life; SwimRight Academy; Bags & The Girl.
**Color analysis:** the concept wardrobe; Gabrielle Arruda; Color Analysis App; Camille
Styles; Color Me Beautiful; Sumissura.
**Critical counterweight:** Everyday Style ("what body-shape advice gets wrong");
Fashion Journal; Urbanite|Suburbanite.
**Academic:** ViBE: Dressing for Diverse Body Shapes (arXiv 1912.06697); Computational
Technologies for Fashion Recommendation: A Survey (arXiv 2306.03395).

*Popular/industry practice synthesized for a casual tool — not medical, fitness, or
peer-reviewed guidance.*
