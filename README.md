# Grow Tree — Design Package

A calm web game: one tree, one watering per day, 2,000+ days of slow, procedural growth.

## Read order
1. `01_GAME_DESIGN_DOCUMENT.md` — vision, loop, watering rules, garden, atmosphere, retention, UI
2. `02_PROGRESSION_MODEL.md` — growth math, life stages, milestones, rare events, Day 1→2000 table
3. `03_PROCEDURAL_TREE_ARCHITECTURE.md` — determinism, personality, skeleton model, rendering, performance
4. `04_STATE_AND_OFFLINE.md` — stored vs derived state, clock/offline rules
5. `05_MVP_SCOPE.md` — MVP feature list vs future
6. `06_HANDOFF_FOR_CLAUDE.md` — brief for the technical-spec writer
7. `AGENTS.md` — standing instructions for OpenCode (copy to repo root)

## One-line concept
Water once a day -> a tiny, permanent change. The tree is a fixed, seeded skeleton that is *revealed* over time, plus age-driven patina, seasons, and rare deterministic events — so day 2000 looks different in *character*, not just size.
