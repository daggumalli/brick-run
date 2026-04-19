# 🧱 BRICK RUN

A satisfying HTML5 mobile runner game where you match brick shapes (and colors) to emoji-shaped wall gaps. Tap to cycle, smash through walls, build combos, chase your best.

**Single HTML file. No build tools. Runs anywhere.**

![Engine](https://img.shields.io/badge/engine-Babylon.js-red)
![Platform](https://img.shields.io/badge/platform-HTML5%20%7C%20Mobile-blue)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 🎮 How to Play

1. **Tap RIGHT** side of screen → cycle brick **shape** (Single, Bar, Square, L, T)
2. **Tap LEFT** side of screen → cycle brick **color** (Level 2+)
3. Match the floating brick's shape (and color in Level 2) to the glowing gap in the approaching emoji wall
4. Brick flies into the wall, docks perfectly, then **BOOM** — wall explodes
5. Miss the match → **CRASH** (with one of 4 dramatic fail animations)
6. Collect coins, build combos, chase your best score

### Controls
| Input | Action |
|---|---|
| Tap right / Space / → | Cycle shape |
| Tap left / Enter / ← | Cycle color (Level 2) |

---

## ✨ Features

### Core Gameplay
- **5 brick shapes** — Single, Bar (1×3), Square (2×2), L-shape, T-shape
- **6 brick colors** — Red, Blue, Gold, Green, Purple, Orange
- **7 emoji-shaped walls** — Smiley, Cat, Robot, Alien, Skull, Heart, Bear
- **2-level progression**
  - **Level 1** (walls 1–10): Shape matching only
  - **Level 2** (walls 11+): Shape + Color matching
- **7 speed tiers** — progressively faster as you clear more walls

### Satisfaction & Juice
- **Dock → Hold → Explode** animation — brick visibly fits into the gap for ~0.2s before the wall shatters
- **4 random crash animations** — Pancake, Bounce, Spin, Launch
- **Particle explosion** with physics (gravity, bounce, rotation, fade)
- **Camera punch** and screen shake on impact
- **Color flash** and vignette pulse
- **Haptic feedback** (mobile)

### Visuals (Babylon.js)
- PBR materials with clearcoat (LEGO plastic look)
- Soft shadows with PCF filtering
- Post-processing pipeline (bloom, FXAA, vignette, ACES tone mapping)
- Glow layer on wall gaps
- Subway Surfers-style scene — trains, trees, buildings, clouds, power lines

### HUD & UX
- Wall counter, coin counter, score display
- Shape indicator bar (Level 1)
- Color dots bar (Level 2)
- Combo text and score popups
- Start screen with best score + total coins
- Game over screen with NEW BEST indicator
- Mobile responsive (375px–430px tested)

---

## 🚀 Quick Start

### Run locally
```bash
# Just open the file in any modern browser
open index.html

# Or serve with any static server
python3 -m http.server 8080
# Then visit http://localhost:8080
```

### No build required
The entire game is in a single `index.html` file. Dependencies are loaded from CDN:
- **Babylon.js** (3D engine) — `https://cdn.babylonjs.com/babylon.js`
- **Google Fonts** (Lilita One) — `https://fonts.googleapis.com`

---

## 📁 Project Structure

```
Brick Dash/
├── index.html            # Complete game (HTML + CSS + JS)
├── README.md             # This file
├── CHANGELOG.md          # Version history
├── DESIGN.md             # Game design document
├── .claude/
│   └── launch.json       # Dev server config for Claude Preview
└── .gitignore
```

---

## 🎨 Architecture

All in one `index.html`:

| Section | Lines (approx) | Purpose |
|---|---|---|
| CSS | 8–45 | HUD, overlays, start/game-over screens |
| DOM | 47–67 | Canvas + HUD elements |
| Constants | 52–160 | Shapes, colors, speed tiers, emoji grids |
| Babylon setup | 162–210 | Engine, scene, camera, lighting, post-processing |
| World building | 212–330 | Road, grass, buildings, trains, trees, clouds |
| Game state | 340–360 | Score, combo, wall tracking |
| Mascot | 365–410 | Runner character (blue bean with crown) |
| Materials | 430–445 | Pooled PBR materials (perf optimization) |
| Wall/brick builders | 450–560 | Emoji wall grid, floating brick, gap glow |
| Input | 565–590 | Tap handlers (left/right zones) |
| Match/crash anims | 700–870 | Core feel — fit, explode, dramatic fails |
| Render loop | 880–900 | Game loop with delta time |

---

## ⚡ Performance

- **Material pooling** — reuses ~15 materials across the scene
- **Mesh recycling** — buildings, trees, trains loop at 250 units
- **Particle cap** — max 120 active particles
- **Shadow map** — 1024×1024 (reduced from 2048 for mobile)
- **Bloom kernel** — 32 (reduced from 64 for mobile)
- **Pixel ratio cap** — max 2× DPR
- **Delta time cap** — 50ms max to prevent tab-switch glitches
- **Target** — 60 FPS on mid-range mobile

---

## 🛠 Tech Stack

- **HTML5 / Canvas / WebGL**
- **Babylon.js** — 3D engine with PBR, post-processing, glow layers
- **Vanilla JavaScript** — no framework, no build step
- **CSS3** — HUD transitions and pulse animations
- **localStorage** — persists best score + total coins
- **Web Vibration API** — haptic feedback on mobile

---

## 📱 Mobile & Deployment

### Browser testing
Works in Chrome, Safari, Firefox (desktop + mobile).

### Viewport
- Portrait-oriented
- Tested on iPhone SE (375px) and iPhone 15 Pro Max (430px)
- `touch-action: none` — no scroll interference

### Wrapping for app stores
The game works as-is inside:
- **Capacitor** — for iOS App Store + Google Play
- **TWA** (Trusted Web Activity) — for Google Play
- **YouTube Playables** — compatible with the HTML5 game spec (pending SDK integration)

---

## 🗺 Roadmap

### Near-term (viral features)
- [ ] Procedural Web Audio sound effects (click, boom, bonk, coin)
- [ ] Slow-motion replay of match moments
- [ ] Shareable end-of-run image card
- [ ] Speed escalation visuals (motion blur, speed lines)
- [ ] Personal-best ghost line marker

### Progression
- [ ] World themes every 10 walls (City → Beach → Space → Lava)
- [ ] Character unlocks via coins
- [ ] Daily challenges
- [ ] Streak milestones (5, 10, 25, 50)

### Social
- [ ] Share run results to TikTok/Reels
- [ ] Leaderboards
- [ ] "Challenge a friend" link

---

## 🧪 Development

### Preview during development
```bash
# Using Claude Preview (if installed)
# Config is in .claude/launch.json

# Or plain Python
python3 -m http.server 8080
```

### Debug in browser
Open DevTools → Console. The game exposes key state on `window`:
- `state`, `score`, `wallsCleared`, `combo`
- `handleTap('left'|'right')` — simulate input
- `currentShapeIdx`, `wallGapShapeIdx`

---

## 📄 License

MIT — see LICENSE file.

---

## 🙏 Credits

Built with [Babylon.js](https://www.babylonjs.com/).
Inspired by Subway Surfers, Stack Ball, and Crossy Road.
