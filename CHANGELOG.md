# Changelog

## v0.3.2 — Player Feedback (2026-04-28)

Direct player feedback after the QA pass surfaced 3 real issues:

### Bug #13 (CRITICAL) — Wall transition flash
After clearing wall 2, the previous wall briefly flashed back into view before wall 3 was built. Cause: `wallRoot.setEnabled(true)` was called BEFORE `generateWall()` — the disabled-but-still-populated wallGroup re-rendered the just-exploded wall for a frame. Fixed by building the new wall first, then enabling.

### Gender variety — Boy/Girl mascot starters
Both KING (👑) and QUEEN (👸) are now free starters. Plus two more girl characters added to the unlock pool:
- **FAIRY** (300 coins) — pink/yellow with light cyan accent
- **MERMAID** (1000 coins) — teal/blue with magenta hair

Girl characters get extra visual elements: long flowing hair on both sides + a pink bow on top. Hair color is per-character (Queen brown, Fairy yellow, Mermaid magenta). Boy characters render without hair/bow.

### Brick-fit satisfaction overhaul
Player report: "the brick and wall fit should be satisfying — that's missing." The dock moment now has:
- **Cartoon overshoot animation** — brick scales 1.0 → 1.22 (impact) → 0.94 (settle) → 1.0 (final), feels like Looney Tunes squash-and-stretch
- **Ring burst particles** — 14 yellow sparks radiate outward from the gap on dock + a center pop cube
- **Brief slow-motion** — 0.08s dt scaling at the moment of fit so the player SEES it
- **Layered dock sound** — sharp 1320Hz click + deep 120Hz bass thud + 440Hz body resonance + brief noise crunch
- **Stronger thud shake** — shakeMag 0.25 → 0.6 plus a camera FOV jolt
- **Longer dock pause** — phase 2 extended from 0.20s to 0.33s in animation time
- **Slower match anim** — `dt*2.2 → dt*1.8` so the entire fit-explode sequence lands properly
- **White impact flash** stacked under the gap-color flash for an extra "POP"
- **Triple haptic pattern** [60,20,30] instead of single 40ms

---

## v0.3.1 — QA Pass (2026-04-28)

Three iterations of pro-QA testing surfaced ~12 issues; all critical and important fixed.

### Critical fixes
- **Bug #9**: `maybeOfferReviveOrFinalize()` fired every frame after `crashAnim.t>=1.2`, stacking event listeners on the revive modal and resetting the auto-decline timer. Now fires exactly once via a `crashAnim.handled` flag.
- **Bug #10**: Document-level `mousedown` handler intercepted clicks on `.char-cell` divs and other clickable UI panels, starting the game instead of unlocking characters / claiming missions. `isUiClick()` now also accepts `.char-cell`, `#missions-panel`, `#chars-panel`, `#revive-modal`.
- **Bug #11**: `document.documentElement.scrollTop` got pushed by 107px on certain interactions, hiding the title and mute button. Fixed by switching overlays to `position:fixed` and adding `position:fixed` to html/body.
- **Bug #12**: Revive YES button charged the player N×50 coins because event listeners stacked across game sessions. Replaced with module-level handlers wired exactly once at boot, plus an `_reviveBusy` re-entry guard.

### Important fixes
- **Bug #1**: Start screen content (~972px) overflowed viewports under 800px tall — bottom 4 characters were unreachable. Made overlays `overflow-y:auto` with proper padding so all content is scrollable on every screen size.
- **Bug #2**: HUD (wall counter, coins, score) was visible behind the start-screen overlay. Now hidden via `body.state-start #hud { opacity: 0 }`.
- **Bug #3**: In-game "TAP!" hint was visible behind the game-over overlay. Now hidden via state-class CSS.
- **Bug #4**: HUD wall counter showed the next wall (e.g., "WALL 2") on game over instead of the cleared count. Now hidden during game over.
- **Bug #6**: Daily challenge mode was non-deterministic — cosmetic effects (combo trail particles, screen shake) consumed the seeded RNG, advancing it differently between runs. Split rand into `rand()` (seeded, used only for world generation: walls, coins, emoji selection, crash type) and `fxRand()` (always Math.random, used for cosmetic particles + shake). Verified bit-for-bit identical 5-wall sequences across two fresh seed runs.

### Architecture / state management
- New `setGameState(s)` helper updates a body class (`state-start`, `state-playing`, `state-gameover`) so CSS can drive HUD visibility, hint hiding, etc. Replaces ad-hoc `state='X'` assignments.
- Boot wires HTML body to `state-start` so new players see the start screen with HUD hidden.

### Pro-QA pass methodology
- 3 test iterations × full feature flow:
  1. Boot, click resolution (chars, missions, daily, mute, share)
  2. Gameplay (NICE/PERFECT/CLUTCH/INSANE tier verification across 8+ walls)
  3. Crash + revive (single-charge, re-entry guard, second-crash falls through to game over)
  4. Daily determinism (seeded sequences match across runs)
  5. Mobile viewport 375×812 (start screen scroll, gameplay, game over)
  6. Full E2E: start → play 5 walls → crash → revive → play 3 more → 2nd crash → final game over
- Result: zero console errors, all flows pass, single -50 charge on revive, body class transitions clean.

---

## v0.3.0 — Hooked Pack (2026-04-27)

The retention layer. Players now have **reasons to come back tomorrow** beyond just chasing a score:

### Daily missions
- 3 deterministic missions per day, seeded by the date so every player today gets the same set
- 11-mission pool covering walls, combos, coins, perfect/clutch/insane matches, daily-mode wins
- Progress tracked in localStorage; persists across runs within the same day
- One-tap CLAIM button awards coins on completion
- Pulsing CLAIM animation draws the eye

### 7-day streak rewards
- Daily-mode runs award escalating coin payouts: 50 / 100 / 150 / 250 / 400 / 600 / 1000 across days 1–7
- Streak resets if you skip a day (loss aversion)
- "Day N streak reward" toast appears on game over

### Character roster
- 8 unlockable mascots: King (free), Pirate (200💰), Astronaut (500💰), Ninja (800💰), Vampire (1.2k💰), Wizard (1.8k💰), Robot (2.5k💰), Champion (5k💰)
- Each character has unique body, belly, and accent (crown) colors applied to the live mascot meshes
- Coins finally have a destination — driving the "grind for the next character" loop
- Tap-to-unlock when affordable; flash-rejection animation when not

### Revive system
- After crashing with ≥3 walls cleared and ≥50 coins, a revive modal appears
- 50-coin cost or auto-decline after 5 seconds
- One revive per run — restores the runner past the failed wall, halves combo
- Prevents the soul-crushing "lost a 30-wall run" experience

### "So close" psychological messaging
- Game over now tracks `lifetimeBestWalls` separately from score-best
- "💔 SO CLOSE — only 2 walls from your best!" message when wallsFromBest ≤ 3
- "Only N walls from your best of M" when wallsFromBest ≤ 8
- "🏆 NEW WALL RECORD" celebration on personal best

### UX polish
- Mission progress bars on start screen (cyan→gold gradient fill)
- Character grid with locked/owned/active states (lock icon, star marker, gold border)
- Coin counter on start screen now uses 💰 emoji consistently
- All new modals respect mute setting + audio context resume

### Total localStorage footprint
| Key | Purpose |
|---|---|
| `brickrun_high` / `brickrun_coins` | Existing best score, coins |
| `brickrun_lt_walls` / `brickrun_lt_fits` / `brickrun_lt_best_combo` / `brickrun_lt_best_walls` | Lifetime stats |
| `brickrun_daily_streak` / `brickrun_daily_last` / `brickrun_daily_best` | Daily streak tracking |
| `brickrun_missions_date` / `brickrun_missions_today` / `brickrun_missions_prog` / `brickrun_missions_claim` | Mission state |
| `brickrun_chars_owned` / `brickrun_char_active` | Character ownership + selection |
| `brickrun_muted` | Mute pref |

---

## v0.2.0 — Viral Pack (2026-04-27)

### Audio
- Web Audio API procedural sounds (no external files)
- 11 signature SFX: tap, cycle, dock (the "fit" click), explode, coin, combo (pitch ramps with combo level), crash, milestone, newbest, clutch, insane
- Mute toggle button (top-right) + `M` keybind, persisted to localStorage

### Wow Callouts (skill-based scoring tiers)
- NICE / PERFECT / CLUTCH / INSANE classification by trigger distance
- Bonus points scale up: PERFECT +5, CLUTCH +15, INSANE +25
- Slow-motion (0.2s for CLUTCH, 0.4s for INSANE) only during match animation
- Color-coded score popups + dedicated SFX per tier

### Milestone Celebrations
- Banner overlays at walls 5, 10, 15, 25, 50, 75, 100 (and every 50 thereafter)
- "NICE!" / "LEVEL 2!" / "ON FIRE!" / "UNSTOPPABLE!" / "LEGENDARY!" / "INSANE!" / "GOD MODE!"
- Each milestone: bouncy text animation, color flash, slow-mo, sound, vibration

### Visual Speed Escalation
- 8 animated speed-line streaks at screen edges
- Sky color subtly shifts warm at high tiers
- FOV widens slightly (1.05 → 1.10)
- Vignette overlay intensifies
- All effects ease-in starting at tier 1.3× — early walls stay clean

### Combo Trails
- Sparkle (gold/white) at combo ≥3
- Flame (orange/red) at combo ≥5
- Rainbow (HSL cycle) at combo ≥10
- Particles emit behind mascot, particle cap raised to 200

### Mascot Personality
- 5 expression states: normal, focused, happy, shocked, dead
- Eye scaling + mouth shape changes per state
- Triggers: focused on match start, happy after dock, shocked on crash, dead at end of crash anim

### Daily Challenge & Retention
- Seeded daily RNG (Mulberry32-ish) — every player gets the same walls today
- 🔥 DAILY CHALLENGE button on start screen
- Streak counter, today's-best tracking
- Lifetime stats: total walls cleared, total fits, best combo ever
- Meta-stats display on start screen as social proof

### Share Mechanism
- "📤 SHARE MY RUN" button on game over
- Generates 1080×1920 PNG share card with mascot, score, walls, CTA
- Web Share API on mobile (native share sheet)
- PNG download fallback on desktop
- New best plays celebration sound

### Visual Polish
- Replaced exponential fog with subtle linear fog (80→180 units) — much clearer near-field
- Reduced vignette weight (1.5 → 0.6) and contrast (1.15 → 1.05)
- Increased exposure (1.1 → 1.15) for richer colors

### Bug fixes
- TAP! hint properly hides after wall 3 (was overridden by CSS animation)
- Score in HUD no longer overlaps with mute button
- visibilitychange handler now resets the correct timestamp variable

---

## v0.1.0 — Initial Release (2026-04-19)

### Game
- Core gameplay loop: auto-runner with shape matching
- 5 brick shapes (Single, Bar, Square, L, T)
- 6 brick colors (Red, Blue, Gold, Green, Purple, Orange)
- 7 emoji wall templates (Smiley, Cat, Robot, Alien, Skull, Heart, Bear)
- 2-level progression: Level 1 shape-only, Level 2 shape+color
- 7 speed tiers with progressive difficulty
- Combo system (up to 10x multiplier)
- Perfect bonus for early matches
- Coin collection on track
- localStorage persistence (best score + total coins)

### Visuals
- Babylon.js 3D engine with PBR materials
- Post-processing: bloom, FXAA, vignette, ACES tone mapping
- Glow layer on wall gaps
- Subway Surfers-style environment (road, trains, buildings, trees, clouds)
- Blue mascot character with golden crown

### Animations
- 5-phase match animation (fly → dock → hold → explode → recover)
- 4 dramatic crash types (pancake, bounce, spin, launch)
- Particle explosion with gravity, bounce, rotation, fade
- Camera FOV tension on wall approach
- Screen shake + color flash on impact
- Squash-stretch on tap

### Input
- Left/right tap zones (color/shape)
- Keyboard support (arrows, space, enter)
- Touch-optimized (passive:false, preventDefault)
- Haptic feedback via Vibration API

### Performance
- Material pooling (reuse per color)
- Shadow caster cleanup on wall dispose
- Particle cap at 120
- 1024 shadow map, bloom kernel 32
- Mesh recycling for buildings/trains/trees
