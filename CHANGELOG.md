# Changelog

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
