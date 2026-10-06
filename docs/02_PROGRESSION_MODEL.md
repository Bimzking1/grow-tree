# 02 — Progression Model

## Two clocks
- **Growth points `P`** (from waterings, incl. Stored Rain): drives size, branching, canopy fullness, milestones.
- **Age `ageDays`** (calendar days since planting, always advancing): drives *patina* — moss, bark ageing, lichen, scars, season. A neglected tree is never smaller-than-it-was, just *older-looking*.

## Size curve (diminishing returns, no endless growth)
```
x        = 1 - exp(-P / 400)
sizeNorm = x ^ 0.6            // fast early visible change, ~saturates by P≈1200
height   = H_min + (H_max - H_min) * sizeNorm
```
| P | 1 | 3 | 7 | 14 | 30 | 60 | 100 | 180 | 365 | 500 | 750 | 1000 | 1500 | 2000 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| sizeNorm | .03 | .05 | .09 | .13 | .21 | .31 | .41 | .54 | .73 | .82 | .91 | .95 | .99 | 1.0 |
After ~P=1000 **size is effectively done**. From there the tree changes *in character* (see Life stages). Trunk girth keeps a slow log term: `girth += 0.02*ln(1+P/1000)`.

## Life stages (visual regimes, blended — never hard cuts)
| Stage | P range | Look |
|---|---|---|
| Seedling | 0–6 | Stem + 2→4 leaves, bare soil |
| Sapling | 7–59 | Thin trunk, first lateral branches, sparse light-green leaves |
| Young | 60–179 | Defined crown, first blossoms/buds, smooth bark |
| Mature | 180–499 | Full dense canopy, fork/leader emerges, first roots show |
| Elder | 500–999 | Furrowed bark, thick limbs, knots, moss patches, canopy begins to open |
| Ancient | 1000+ | Sculptural: layered "cloud" canopy with gaps showing structure, deadwood, hollow, scars, lichen, luminous accents |
Key anti-"just bigger": **canopy density peaks around Mature then thins/layers** in Elder/Ancient, revealing the skeleton; character features accumulate instead of scale.

## What changes — three layers
### A. Continuous growth (every watering, tiny)
trunk height & thickness, branch length/thickness (per-node growth over its own timeline), new twig tips appearing, leaf count -> peak, leaf size, canopy envelope radius, root flare, slow bark darkening, canopy colour drift.
### B. Milestone transformations (deterministic thresholds on P or age)
| Trigger | Transformation |
|---|---|
| P=1 | Sprout |
| P=3 | First true leaves |
| P=7 | First lateral branch |
| P=14 | Second branch tier |
| P=30 | Lantern appears; first buds |
| P=60 | First blossoms (season-aware); fireflies unlocked |
| P=100 | Pond appears; first fruit/seed pods (if trait); bird visits |
| P=180 | Leader fork / second limb; roots surface |
| P=365 | **Garden-year**: first growth ring; bark furrows begin; anniversary ceremony |
| P=500 | First knot/hollow starts; canopy begins opening |
| P=750 | Aerial roots / hanging moss; wider limbs |
| P=1000 | **Thousandth Watering**: heartwood glow, luminous accents, "Ancient" begins |
| P=1500 | Layered canopy sculpting, lichen, weathered limb |
| P=2000 | **Signature bloom**: petals continuously drift; halo-light at dusk; Legend badge in journal |
| Age 120/240/… | Moss coverage steps (smoothstep over days 60–900) |
| Rhythm 7/30/100 | Cosmetic: fireflies/petal-ring (permanent once earned) |
Optional future: after P=2000, *Seedling* — collect a seed to plant a second tree.
### C. Rare events (deterministic from `hash(seed, dayIndex)`, only realised on watered days, ≥7-day spacing)
rainbow after rain · nest & chicks · butterfly swarm · koi surfaces · petal storm · shooting star · mushroom ring · golden-hour glow · **storm snaps a branch** (very rare, ~1/400 days, leaves a permanent scar + regrowth shoot) · snow in garden-winter. Event weights vary by personality/rare traits. Because they're pure functions of (seed, day), they never need to be stored — only "seen" flags for toasts.

## Seasons
- Modes: `garden` (default), `real-north`, `real-south`.
- **Garden year = 120 days (30/season)** — tunable constant; equatorial players still get seasons. 2000 days ≈ 16 garden-years. `real-*` uses actual date.
- `seasonPhase = (ageDays mod YEAR)/YEAR`, blended with smoothstep. Drives leaf colour (green -> amber -> bare -> bud -> blossom), leaf density, light angle/colour, particles (petals/leaves/snow), fireflies.

## Day 1 → 2000 visual milestones (assumes near-daily watering; P≈day)
| Day | What you see |
|---|---|
| 1 | A single pale stem and two cotyledon leaves in a dark soil mound, dew on soil. Dawn light. |
| 3 | Stem slightly taller, first true leaves unfold; tiny sway in wind. |
| 7 | First side shoot; trunk visibly woody at base. Reads as a sapling. |
| 14 | Two tiers of branches, ~15 leaves; first stepping stone appears. |
| 30 | Slender young tree, lantern lights at dusk, first buds; personality (lean/shape) now obvious. |
| 60 | First blossoms or colour accents; fireflies at night; canopy outline recognisable. |
| 100 | Pond appears, bird visits; crown fuller; smooth bark with first lenticels. |
| 180 | Leader fork or second limb; roots begin to surface; canopy dense and rich. |
| 365 | Garden-year ceremony: first growth ring, bark furrows, thicker trunk, season-cycled foliage, moss at base. |
| 500 | Knots and a small hollow forming; canopy begins to open; moss on north side. |
| 750 | Aerial roots/hanging moss, heavy limbs, mossy rocks, fallen leaf litter pattern. |
| 1000 | Thousandth watering: soft inner glow in the heartwood, luminous leaf accents, tree reads "old". |
| 1500 | Cloud-layered canopy with negative space, lichen, weathered/scarred limb, deep furrows, mossy roots. |
| 2000 | Ancient sculptural tree; signature bloom drifting petals; halo of light at dusk; garden fully matured; journal "Legend". |

## Recommended progression model (summary)
`P` (waterings + Stored Rain) -> `sizeNorm` curve + life-stage blend. `ageDays` -> patina + season. `seed` -> personality + skeleton + events + weather. Milestones = thresholds. Rare events = hashed calendar. Everything *derived* from `(seed, ledger, today)`.
