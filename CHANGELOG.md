# Changelog

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
