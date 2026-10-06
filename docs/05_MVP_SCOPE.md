# 05 — MVP Scope

**Goal:** prove "I want to check my tree today" with the smallest build. A solo dev + AI should finish this.

## MVP features
**Game logic**
- [ ] Day clock (04:00 rollover, injectable now), once-per-day watering
- [ ] Ledger, growth points, Stored Rain, sizeNorm curve, life-stage blend
- [ ] Seeded personality (6 archetypes, 3 rare traits)
- [ ] Skeleton generator (trunk + 2–3 branch orders, birthP reveal)
- [ ] Garden-year seasons (garden mode only)
- [ ] Milestones P=1,3,7,14,30,60,100,180,365 (+ stub for later) with journal lines
- [ ] Welcome-back tiers (1 day / few days / long absence)
- [ ] Deterministic daily weather (clear / cloud / rain / mist)
- [ ] 3 rare events (rainbow, butterflies, petal drift)
- [ ] Local save, backup slot, export/import JSON, migrations scaffold
**Rendering**
- [ ] Procedural branches (merged geometry) + instanced leaves + wind shader
- [ ] Seasonal leaf colour + falling leaves
- [ ] Garden: soil mound, sand ring, 3 rocks, grass tufts, lantern (P30)
- [ ] Day/dusk/night lighting by local time, soft shadows
- [ ] Orbit-limited camera with auto-framing
- [ ] Watering animation + growth morph (uniform tween or crossfade) + milestone chime/toast
**UI**
- [ ] "Day N" label -> Journal drawer; hold-to-water button; "Rested until tomorrow"; settings (sound, reduced motion, export/import, reset)
**Quality**
- [ ] Vitest for game/ (day boundaries, DST, clock rewind, determinism snapshots)
- [ ] Dev "Time Machine" panel to preview any day/P/season/seed

## Explicitly NOT in MVP (Future)
Pond & koi · fireflies · fruit · roots/aerial roots · hollow/knots/scars/storm-branch · garden finds · real-hemisphere seasons · snow · full sound design · PWA install/reminder · Ancient-stage canopy sculpting & luminous traits · second tree "Seedling" · screenshots/share card · cloud sync · accessibility polish beyond basics · localisation.

## MVP success test
Can the 30-second daily ritual feel good, and does Day 30 look clearly different from Day 7 and Day 1? If yes -> build Future items in this order: seasons polish -> rare events -> pond/fireflies -> Elder/Ancient features -> PWA.

## Suggested build phases
0. Scaffold (Vite+React+TS+Tailwind+R3F+Drei+Zustand+Vitest)
1. `game/` logic + tests (no 3D)
2. Skeleton generator + debug viewer with P slider
3. Leaves, seasons, wind
4. Garden props + lighting + weather
5. Watering FX + growth animation + UI + journal
6. Persistence, offline, welcome-back flows
7. Polish, perf pass, mobile test
