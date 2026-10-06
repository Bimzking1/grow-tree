# 06 — Handoff to Claude (Technical Implementation Specification Author)

You are receiving a completed game/systems design. **Do not redesign.** Convert it into a technical implementation spec that OpenCode can execute step by step.

## Deliver
1. **Repo layout & tooling:** Vite + React + TS (strict) + Tailwind + three + @react-three/fiber + @react-three/drei + zustand + vitest. ESLint/Prettier. Path aliases. Scripts.
2. **Type definitions:** `SaveV1`, `DerivedState`, `Personality`, `SkeletonNode`, `Milestone`, `RareEvent`, `WeatherKind`, `Season` — from docs 02–04.
3. **Pure function contracts** for every file in `src/game/` (signature, inputs, outputs, invariants, edge cases) incl. exact formulas: sizeNorm, Stored Rain, vitality, seasonPhase, stage blend.
4. **PRNG spec:** hash function, keyed-stream API, list of stream names, determinism test vectors.
5. **Skeleton generator spec:** parameters from personality -> algorithm steps -> node schema -> birthP assignment -> acceptance tests (node count range, no NaN, stable hash).
6. **Render spec:** geometry builder (tube attributes), growth shader (`uP`, `aBirth`), leaf instancing, bark/moss shader uniforms, garden slots, lighting rig, camera rig, particle pools, quality tiers, disposal rules.
7. **UI spec:** components, states (not watered / watered / welcome-back / milestone), tailwind tokens (muted palette), a11y (focus, reduced motion, hold-to-water alternative: keyboard Space hold or 3-tap accessible mode).
8. **Persistence spec:** adapter interface, atomic save, backup, migrations, export/import, quota/private-mode handling, `visibilitychange` hooks.
9. **Time/clock spec:** 04:00 rollover, DST-safe day diff, clock-rewind via `highWaterDayIndex`, polling.
10. **Task breakdown:** ordered, small, independently verifiable tasks (each with acceptance criteria) following the phases in doc 05.
11. **Test plan** and **perf budget checks**.

## Constraints to preserve
- Game logic is pure and independent of React/three.
- Everything visual is derived from `(seed, ledger, now)`.
- No `Math.random` in gameplay/tree. Keyed PRNG streams only.
- No procedural re-roll on render; skeleton cached by `(seed, generatorVersion)`.
- No external 3D assets. No backend.
- Tone: calm, premium, no childish UI; never punish absence.

## Tunable constants (put in one `constants.ts`)
`DAY_ROLLOVER_HOUR=4`, `GROWTH_TAU=400`, `SIZE_EXP=0.6`, `STORED_RAIN_PER_DAY=0.2`, `STORED_RAIN_CAP_DAYS=7`, `GARDEN_YEAR_DAYS=120`, `RHYTHM_WINDOW=14`, `VITALITY_FLOOR=0.6`, `EVENT_MIN_GAP_DAYS=7`, `MAX_LEAF_INSTANCES=7000`, `HOLD_TO_WATER_MS=1200`.

## Open questions to resolve (propose defaults, don't block)
- Leaf style: curved card vs low-poly blob (recommend card + soft shading).
- Growth anim: GPU `uP` tween (recommended) vs crossfade fallback.
- Audio: WebAudio synthesis vs small audio files.
