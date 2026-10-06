# 01 — Game Design Document

## Pillars
1. **The tree is the protagonist.** Everything else is quiet supporting cast.
2. **"My tree grew."** The only core reward. No currencies, energy, ads, loot.
3. **Gentle, never punishing.** Absence is never a failure state; the tree rests.
4. **Slow magic.** Small daily change, big surprises at milestones and rare moments.
5. **Identity.** Your tree is yours: seeded, persistent, recognisable on day 5 and day 1500.

## Day boundary
- A "day" = local calendar date, **rolling over at 04:00 local time** (night owls can still water "tonight"; avoids midnight edge cases).
- `dayKey` = `YYYY-MM-DD` of (now − 4h). `dayIndex` = whole-day difference between local dates since planting (calendar math, NOT 24h arithmetic -> DST-safe).

## Core loop — what happens when…
| Situation | Behaviour |
|---|---|
| **First ever open** | Short, wordless intro: bare soil mound at dawn. Tap to plant seed (seed generated, personality rolled silently). Immediately offer first watering -> sprout appears. Day 1 ends with a "come back tomorrow" glimpse (a bud-like glint, not text-heavy). |
| **Open, not watered today** | Tree idle, soft "thirsty" cue: soil slightly dry/pale, a gentle glint on the watering vessel, leaves a touch less vibrant. Water control is the single prominent action. No timers, no red badges. |
| **Water the tree** | Press-and-hold ~1.2s (or tap-and-hold on the vessel). Water pours, soil darkens, ripple, light travels up the trunk, tips glow, 3–4s "growth moment" (yesterday's silhouette morphs into today's). Toast only if a milestone/rare event fires. State saved instantly. |
| **Already watered today** | Tree content: soil damp, tiny sparkles on new tips. Control becomes quiet text: "Rested until tomorrow". Player can still orbit, watch weather, open journal, take a photo. |
| **Return tomorrow** | New day detected on load/focus -> "Good morning" settle: soil dries slightly, tree shows overnight passive changes (new moss speck, leaf drift). Water control returns. |
| **Return after several days (2–13)** | "Welcome back" whisper: *"The garden kept its rhythm. N days passed."* Passive age changes visible (season shifts, moss, fallen leaves). On watering: **Stored Rain** bonus (see below). |
| **Return after months** | Calm "Reawakening" sequence: camera slow dolly-in, season has moved on, leaves/blossoms per current season, accumulated moss/fallen leaves. Journal gets a "While you were away" entry summarising seasons passed, one rare event retroactively *noticed* (e.g. a nest now present). First watering triggers a special reawakening animation + full Stored Rain cap. **No loss of size, no dying.** |

## Daily watering rules
- **Once per dayKey.** Not a 24h cooldown (cooldowns drift and feel arbitrary; calendar days build ritual).
- **Base gain = 1.0 growth point.**
- **Stored Rain (catch-up):** if `missedDays >= 1`, bonus = `min(missedDays, 7) * 0.2` -> max +1.4. Softens absence without rewarding it more than showing up (daily watering over 7 days = 7.0; returning after 7 missed = 2.4).
- **Diminishing returns:** not on the gain, but in the **growth curve** (see doc 02) — each point visually matters less over time, while *character* changes accelerate via milestones/age.
- **Streaks:** do not affect size. A soft **Rhythm** value (waterings in last 14 days / 14) only drives *vitality* (leaf lushness/colour vibrancy), floor at 0.6 so the tree never looks sick, recovers fully within ~3 days of resuming. Breaking a streak resets nothing permanent. Rhythm milestones (7/30/100 consecutive) trigger purely cosmetic rewards (fireflies linger, petals) that are **never lost**.
- **No withering, no death, no scoreboard of missed days.**

## Retention without aggressive gamification
- **Visible daily change** (even if tiny) + **a hint of tomorrow** ("buds are swelling" when a milestone is within 1–3 waterings).
- **Daily deterministic weather** (seed + dayKey): same day always same sky; the garden is never identical twice.
- **Rare events on watering** (~1 per 10–14 days): rainbow, nest, butterflies, koi, petal drift, shooting star. Surprise > schedule.
- **Seasons** that really change the look.
- **Milestone ceremonies** at round waterings and anniversaries (garden-year).
- **Journal**: short, poetic, auto-written entries ("Day 112 — A wren visited at dusk.") Becomes a keepsake.
- **Garden finds**: ~5% chance per watering a small stone/shell/feather appears in a fixed slot (max ~8 total) — decorative, no menu.
- **Opt-in only**: one optional gentle PWA reminder (future). Default off.
- Never: energy, ads, loot boxes, daily quests, streak-loss warnings, guilt copy.

## Zen garden (supporting cast)
Circular garden island on a soft backdrop. Hard cap ≈ 8 visible props.
| Element | Notes | Unlock |
|---|---|---|
| Moss mound + soil at base | tree anchor | Day 1 |
| Raked sand ring (shader-pattern rings) | slowly "re-raked" each day (pattern phase shift) | Day 1 |
| 2–3 rocks (seeded layout) | one large, two small | Day 1 |
| Grass tufts (instanced, sparse) | wind-swayed | Day 1 |
| Stepping stones | 3–5 | W10 |
| Stone lantern | warm light at dusk | W30 |
| Small pond (+ lily, koi rare) | reflection-lite | W100 |
| Fallen leaves / petals | seasonal, pooled particles | seasonal |
| Fireflies | dusk/night, summer-ish | W60 + seasonal |
| Surfacing roots / moss on roots | tree-derived | W180+ |
| Wind / rain / sun shafts / shadows | weather system | Day 1 |
Placement uses seeded **slots** (fixed positions per seed), so props never jump.

## Atmosphere
- **Camera:** perspective, FOV ~32–38°, 3/4 view from slightly above, limited orbit (±60° yaw, small pitch), damped drag, pinch/scroll zoom clamped. Auto-frames tree height with smooth dolly as it grows. Slow idle breathing drift. Reset by double-tap.
- **Lighting:** one warm directional sun (soft shadows), hemisphere ambient, subtle fog. Time-of-day follows real local time in 4 palettes (dawn, day, golden hour, night) with smooth blends. Optional light bloom only on lantern/fireflies.
- **Palette/style:** muted, desaturated naturals, soft stylised low-poly with smooth shading, paper-grain overlay; no cartoon outlines, no bouncy UI, no primary colours.
- **Ambient motion:** wind sway (vertex shader, branch-weighted), drifting dust motes, leaf falls, pond ripples, cloud shadow drift. `prefers-reduced-motion` -> halve motion, skip camera drift.
- **Sound concepts:** soft wind bed, distant water, sparse wooden chime/bell on milestone, gentle pour on watering, birdsong at dawn, crickets at night, muffled rain. Off until first interaction; one mute toggle. Tiny files or synthesised via WebAudio.
- **Watering animation:** ceramic vessel tilts in from corner, thin stream, ripples on soil, soil darkens, droplets, a soft pulse travels trunk -> canopy.
- **Growth animation:** ghost silhouette of the previous state eases to the new one; new tips emit faint light motes. Daily change is subtle; milestones get a slow camera push and a chime.
- **Particles:** water droplets, light motes, petals, leaves, fireflies, rain — all instanced/pooled, hard-capped.

## UI (minimal, scene-first)
- **Top-left (tiny):** "Day 47" — tap opens the Journal drawer.
- **Bottom-centre:** the one action: *Hold to water* ring-button. After watering: "Rested until tomorrow".
- **Milestone toast:** one poetic line, auto-dismiss ~4s.
- **Journal drawer:** timeline of milestones/events/finds + small growth-ring graphic (one ring per garden-year). No stats grid.
- **Settings (gear):** sound, reduced motion, season mode, export/import save, reset.
- No dashboard, no XP bars. Optional subtle "growth glow" ring around the button instead of a progress bar.
