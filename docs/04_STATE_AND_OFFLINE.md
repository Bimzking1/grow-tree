# 04 — Game State & Offline Behaviour

## Stored (minimal, append-only where possible)
```ts
interface SaveV1 {
  schemaVersion: 1;
  generatorVersion: 1;          // bump only when skeleton algorithm intentionally changes
  treeSeed: string;             // all personality/skeleton/events derive from this
  plantedOn: string;            // local date "YYYY-MM-DD" (day-0 reference)
  tz: string;                   // IANA tz at planting (informational)
  ledger: Array<[dayIndex: number, gainX100: number]>; // one entry per watering; gain frozen at time of watering
  lastWateredAt: string;        // ISO timestamp (display only)
  highWaterDayIndex: number;    // max dayIndex ever observed (clock-rewind guard)
  seen: { milestones: string[]; events: number[]; };    // toasts already shown
  finds: Array<[dayIndex: number, slotId: number]>;     // garden finds collected
  settings: { seasonMode: 'garden'|'real-north'|'real-south'; sound: boolean; reducedMotion: boolean; quality: 'auto'|'low'|'med'|'high'; };
}
```
~2000 waterings ≈ 20–30 KB JSON. Fine for localStorage; IndexedDB-ready.

## Derived (never stored)
`ageDays`, `totalWaterings` (= ledger.length), `growthPoints P` (= Σ gain), `sizeNorm`, `stage`, `season/seasonPhase`, `vitality/rhythm`, `streak`, `personality`, `skeleton`, achieved milestones (by P/age), today's weather, today's rare event, `canWater`, moss/patina amounts.
> Why a ledger with frozen gains: rebalancing the formula later never rewrites a player's history.

## Offline / local-first
- Zero backend. All logic runs client-side; assets procedural -> tiny bundle. Optional PWA (service worker + manifest) so it installs and loads offline.
- **Save on every watering** and on `visibilitychange`/`pagehide`. Write atomically (serialise, then set; keep one `backup` slot).
- **Browser closed/reopened:** load save -> `derive(state, now)` -> compute days passed -> run Welcome-back flow.
- **Tab left open across midnight(04:00):** poll every 60s + on `visibilitychange`/`focus`; when `dayKey` changes, fire "Good morning" and unlock watering.
- **Opened on another day:** same as reopen; gap size selects the welcome-back tier (1 day / few days / weeks / months).
- **Clock changes:**
  - Forward (any size): treated as real time passing (tree ages, seasons move); Stored Rain capped, so no exploit that matters.
  - Backward: `today = max(computedToday, highWaterDayIndex)`. If already watered on that day, remain "rested"; never decrease age or P. Optionally show "The garden is still resting."
  - Timezone travel/DST: day math uses local calendar dates; occasional double/skipped day tolerated (anti-cheat not a goal).
- **Corruption/missing storage:** fall back to backup; else offer Import; else new tree. Private-mode/quota errors -> run in memory with a quiet "progress won't be saved" note.
- **Export/Import:** download/upload JSON (also the future migration path to cloud).
- **Migrations:** `schemaVersion` + ordered migration functions; never break saves.
