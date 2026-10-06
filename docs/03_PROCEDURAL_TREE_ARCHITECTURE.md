# 03 — Procedural Tree Architecture

## Core idea: **a fixed mature skeleton, progressively revealed**
Never regenerate a different tree per age. Instead:
1. From `treeSeed` + `personality`, generate **once** a full "destiny skeleton" (target P_max≈2000) as a graph of nodes.
2. Each node stores `birthP` (when it starts growing), `growDuration`, parent, position/direction at full growth, radius, `order` (trunk=0, limb=1, branch=2, twig=3).
3. At any state, node visible length/radius = `ease((P − birthP)/growDuration)` clamped 0..1 (+ age girth term). Nodes with `birthP > P` don't exist yet.
=> The tree keeps its identity forever; new growth just appears at the tips and thickens inside.
Skeleton generation is pure, cached by `seed`, ~3–6k nodes, <50 ms.

## Deterministic randomness
- PRNG: `mulberry32` or `sfc32` seeded from a string hash (`cyrb128`).
- **Never use one sequential stream.** Use **named, keyed streams**: `rng(seed, "trunk")`, `rng(seed, "branch", nodeId)`, `rng(seed, "leaf", nodeId, i)`, `rng(seed, "event", dayIndex)`, `rng(seed, "weather", dayIndex)`. Adding a feature later won't shift other results.
- No `Math.random()` in game logic or tree rendering. Render-only cosmetics (droplets) may use it.
- Snapshot tests: hash of skeleton for fixed seeds must stay stable across refactors (bump `generatorVersion` intentionally).

## Generation algorithm (recommended)
Structured growth (not pure L-system, not space colonization for MVP):
- **Trunk:** N segments, direction perturbed by `lean`, `twist`, `wobble` (low-frequency noise from seeded stream), tapering radius (pipe-model: parent radius² ≈ Σ child radius²).
- **Branching:** at internodes, spawn lateral branches with probability `branchChance`, angle `branchAngle ± variance`, phyllotaxis-like yaw rotation (golden angle + jitter), length scaled by `apicalDominance` and height-from-base profile (`crownProfile(t)` shaped by canopy type).
- **Tropisms:** phototropism (bend up), gravitropism (weeping droop for weepers), asymmetry bias (`windDir` / `sunSide`).
- **Canopy envelope:** a signed-distance-ish function per canopy type; terminal twigs outside the envelope are shortened/pruned deterministically.
- **birthP assignment:** by depth/order: trunk early, limbs mid, twigs late; randomised within bands; scaled so full structure completes by P≈1000, fine twigs/deadwood detail by 2000.
- **Ageing features** (keyed to node/seed): knot sites, scar sites, hollow site, deadwood branches (a few twigs flagged `deadAtP`), aerial-root sites. Activated by age/P thresholds, so they appear on the *same* spots every time.

## Personality (rolled once at planting, stored as seed only; params recomputed)
```
personalityFromSeed(seed) ->
  archetype      (weighted)
  lean, twist, wobble
  apicalDominance, branchChance, branchAngle, branchVariance
  crownType: ovoid | umbrella | vase | cone | cloud-layered | weeping
  asymmetry, density, leafSize, leafShape
  hues: leafHue, autumnHue, barkHue, blossomHue
  traits[]  (rare)
```
**Archetypes (weights):** Upright Slender 18% · Wide Canopy 18% · Crooked/Windswept 16% · Asymmetrical 12% · Heavily Branched 12% · Sparse Sculptural 10% · Weeping 8% · Ancient-looking (gnarled early) 6%.
**Rare traits (roll independently):** Silver bark 8% · Twin trunk 6% · Winter blossom 4% · Golden autumn 6% · Luminous leaves (night glow) 3% · Heart-hollow 5% · Crimson leaves 4% · Mythic (two traits) ~1%. Traits shape rare-event tables and visuals.
Hue/param ranges are clamped to a curated palette so every tree looks tasteful.

## Rendering architecture (Three.js primitives only)
- **Branches:** one merged `BufferGeometry`: tapered tubes per node (5–8 radial segments, 2–4 length segments by order), attributes `aBirth`, `aT`(along-branch), `aOrder`, `aNodeId`. A vertex-shader uniform `uP` (current growth points) computes per-vertex growth -> **animate growth on GPU by tweening `uP` from old to new** (no CPU rebuild). Fallback (simpler MVP): rebuild geometry on state change and cross-fade.
- **Bark:** procedural shader: noise furrows blended by age, moss by `uMoss` (up-facing/north-facing + near base), lichen speckles, silver/hue tint.
- **Leaves:** `InstancedMesh` of a small curved leaf card (or tiny icosa blob clusters for stylised look). Instances placed on twig tips via keyed streams; per-instance `birth`, `size`, `colourJitter`. Season/vitality via uniforms. Cap ≈ 6–8k instances. Wind in vertex shader.
- **Blossoms/fruit:** second small instanced set, shown by season/trait.
- **Roots, rocks, lantern, pond:** custom geometry (lathe, displaced icosahedron, boxes, extrude). Rocks = noise-displaced icospheres.
- **Sand rings:** a plane with a ring-pattern fragment shader. **Sky:** gradient dome + fog.
- **Particles:** pooled `Points`/instanced quads (leaves, petals, fireflies, droplets, rain).

## Performance guidance
- Budget: ≤ ~80k tris, ≤ ~120 draw calls, 60fps on mid phones; DPR clamp 1.5–2; one shadow map (1024–2048), tree + rocks only.
- Build geometry **only when seed/generatorVersion changes**, never per frame. Dispose geometries/materials on unmount.
- Frame loop: `frameloop="demand"` when idle & no animation, otherwise always; pause on tab hidden.
- Keep game logic out of `useFrame`; only animate uniforms there.
- Graceful quality tiers (low/med/high) toggling leaf cap, shadows, particles.

## Component/code architecture
```
src/
  game/        pure TS, zero React/three imports (unit-testable)
    clock.ts       dayKey/dayIndex (04:00 rollover), injectable now()
    rng.ts         hash, mulberry32, keyed streams
    personality.ts personalityFromSeed
    growth.ts      sizeNorm, vitality, stored rain, stage blend
    milestones.ts  thresholds, journal entry text
    events.ts      rare events / weather / finds (pure f(seed, dayIndex))
    season.ts      seasonPhase, modes
    skeleton.ts    generateSkeleton(seed, personality) -> Node[]
    state.ts       types, derive(state, now) -> DerivedState, water(state, now)
  persistence/ storage adapter (localStorage now, IndexedDB-ready), migrations, export/import
  store/       zustand store wrapping game/ (single source of truth)
  render/      react-three-fiber components: Scene, TreeMesh, Leaves, Garden, Sky, Weather, CameraRig, WaterFX
  ui/          Hud, WaterButton, Journal, Toast, Settings (Tailwind)
  dev/         "Time Machine" debug panel (day/P slider, force season/event) — dev only
```
Rule: **render consumes `DerivedState` only.** `derive()` is pure: `(storedState, now) -> {ageDays, P, sizeNorm, stage, season, vitality, moss, todayEvent, canWater, ...}`.

## Extensibility hooks
Species presets (parameter packs), new rare events (add a table row), new garden props (add a slot + unlock rule), `generatorVersion`/`schemaVersion` migrations, second-tree "Seedling" mode, backend sync later via same JSON save.
