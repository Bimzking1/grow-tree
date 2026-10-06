# AGENTS.md — Instructions for OpenCode (Grow Tree)

## Project
Web game "Grow Tree": one procedural tree, watered once per local calendar day, 2,000+ days of progression. Stack: React, Vite, TypeScript (strict), Tailwind, three, @react-three/fiber, @react-three/drei, zustand, vitest. Local-first, no backend, no external 3D assets.

## Source of truth
Read `docs/` in order: 01 GDD -> 02 Progression -> 03 Procedural architecture -> 04 State -> 05 MVP -> 06 Handoff. If a technical spec (from Claude) exists in `docs/tech-spec/`, follow it first; otherwise follow the design docs.

## Hard rules
1. `src/game/` is pure TypeScript: no React, no three, no DOM, no `Math.random`, no `Date.now()` (inject a clock).
2. Rendering consumes `DerivedState` only. Never put game rules in components.
3. Randomness = keyed deterministic streams `rng(seed, name, ...ids)`.
4. The tree is a fixed seeded skeleton revealed by growth points `P`; never regenerate a different tree per age.
5. Never punish absence: no shrinking, no death.
6. Keep MVP scope (doc 05). Do not build Future items unless asked.
7. Add Vitest tests for every `game/` module; add determinism snapshot tests.
8. Work in small steps following the build phases; after each, run `npm run test` and `npm run build`.
9. Keep one `constants.ts` for all tunables.

## Start here
Phase 0 scaffold -> Phase 1 game logic + tests -> Phase 2 skeleton + debug viewer with day slider. Stop and report after each phase.
