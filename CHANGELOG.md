# Changelog

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
