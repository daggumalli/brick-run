# 🎮 BRICK RUN — Game Design Document

## Vision

A **hyper-casual mobile runner** with a distinctive twist: instead of swipe-dodging obstacles, you **match** shapes and colors to gaps in emoji-faced walls. The satisfaction comes from the **perfect fit moment** — the brick docking into the gap — then the **explosive release** of the wall shattering.

**One-sentence pitch:** *Subway Surfers meets LEGO Bricks — tap to match, smash to break, chase your best.*

---

## Core Loop

```
  ┌──────────────┐     ┌───────────┐     ┌──────────────┐     ┌──────┐
  │ Wall appears │ ──▶ │ Tap to    │ ──▶ │ Brick docks  │ ──▶ │ BOOM │
  │  ahead       │     │ match     │     │ into gap     │     │      │
  └──────────────┘     └───────────┘     └──────────────┘     └──┬───┘
         ▲                                                         │
         │                                                         │
         └─────────── combo++ · coins · speed up ◀─────────────────┘
```

A single wall takes ~3–4 seconds. The loop is **short, clear, rewarding**.

---

## Design Pillars

### 1. **Satisfaction First**
Every wall should end in a dopamine hit. The brick doesn't just disappear into the wall — it **visually seats** into the gap for ~0.2s before the wall shatters. Player sees: "It fits. Perfect. Now BOOM."

### 2. **One-thumb playable**
No swipes, no multi-touch, no complex gestures. Just **tap**. Left side = color, right side = shape. Works one-handed on a phone while standing in a subway.

### 3. **Instant restart**
Death → game over screen → tap → playing. No loading, no waiting. The "one more try" loop must be friction-free.

### 4. **Visual clarity under motion**
The player is moving forward fast. Walls must be **instantly readable** — emoji shapes are universally recognized, glowing gaps are hard to miss, brick colors are bold primaries.

### 5. **Progressive difficulty curve**
Level 1 (walls 1–10): learn shape matching. Level 2 (walls 11+): add color matching. Speed ramps across 7 tiers. No sudden cliffs — always "just a bit harder."

---

## Systems

### Shape System
5 brick shapes on a 2-row grid:

| Shape | Layout | Cells |
|---|---|---|
| SINGLE | `▪` | 1 |
| BAR | `▪▪▪` | 3 |
| SQUARE | `▪▪`<br>`▪▪` | 4 |
| L | `▪`<br>`▪▪` | 3 |
| T | `▪▪▪`<br>`⠀▪⠀` | 4 |

### Color System
6 bold primaries: Red, Blue, Gold, Green, Purple, Orange.
Introduced at wall 11 as Level 2.

### Emoji Wall System
Each wall is a 9×9 grid encoding an emoji face. The gap shape is carved from the mouth region.

**Emojis:** 😊 Smiley · 🐱 Cat · 🤖 Robot · 👽 Alien · 💀 Skull · ❤️ Heart · 🐻 Bear

Randomly selected per wall. Gap position is dynamically centered in the mouth region based on the current gap shape's dimensions.

### Speed Tiers

| Walls | Speed | Spawn distance |
|---|---|---|
| 1–5 | 1.0× | 60 units |
| 6–10 | 1.15× | 55 |
| 11–15 | 1.3× | 50 |
| 16–20 | 1.5× | 45 |
| 21–30 | 1.75× | 40 |
| 31–50 | 2.0× | 35 |
| 51+ | 2.5× (cap) | 30 |

### Scoring

| Event | Points |
|---|---|
| Wall cleared | 10 × combo |
| Perfect (trigger dist > 5 units) | +5 × combo |
| Coin | +1 (tracked separately) |

**Combo** increments per cleared wall (max 10×). Resets on crash.

### Match vs Crash Logic

```
if distToWall ≤ 8 and shape matches and (Level 1 OR color matches):
    → triggerCollapse()
elif distToWall ≤ 0:
    → triggerCrash()
```

---

## The Money Moments

### 🧩 The Fit
5-phase sequence on match:
1. **Fly** (0→0.25): Brick smoothsteps from in front of runner to gap center, camera FOV narrows.
2. **Dock** (0.25→0.45): Brick seats inside the gap. Gentle thud, 40ms haptic. Player SEES the fit.
3. **Explode** (0.45→0.5): Color flash, big shake, `spawnExplosion()` — every wall brick becomes a flying particle.
4. **Recover** (0.5→1.0): Debris flies with gravity + bounce, camera FOV eases back.
5. **Next wall** (at t=1.0): Score popup, combo text, generate next wall.

### 💥 The Crash
4 randomized fail animations:
- **Pancake** — mascot slams flat against wall, slides down
- **Bounce** — impact squash, then arcing tumble backward
- **Spin** — spins wildly like a top, shrinks away
- **Launch** — squashes, then flies off screen upward with backflips

Each crash is a **shareable TikTok moment** — dramatic, funny, different every time.

---

## Character

**The Blue Prince** — a chunky round mascot with:
- Egg-shaped blue body with lighter blue belly
- Golden crown (5 points + cylinder base)
- White shoes
- Face features on +z (front) — forward-facing during run
- Runs animation: slight vertical bob synced to speed

---

## World

**Setting:** A stylized endless city road.
- **Road:** Dark asphalt with white dashed lane markings, orange curbs
- **Grass:** Deep green, both sides
- **Sky:** Blue gradient dome, not infinite (follows runner)
- **Fog:** Exponential, matches sky color
- **Populated with:** Colorful Lego-style buildings (windows, roof studs), train cars with yellow stripes, spherical trees, puffy clouds, power-line poles

Elements **recycle** — when they fall behind the runner, they jump forward by TRACK_LEN.

---

## Visual Style

Inspired by the LEGO cinematic universe and Fall Guys:
- **PBR materials** with slight clearcoat → plastic sheen
- **ACES filmic tone mapping** → warm, cinematic colors
- **Bloom** → glowing gap + coins
- **Vignette** → focus center
- **Soft shadows** (PCF filtering)
- **Color palette:** Subway Surfers primaries — saturated but warm, not neon

---

## Controls Philosophy

**Why split the screen into left/right tap zones?**

| Alternative | Why we rejected it |
|---|---|
| One button for everything | Can't progressively add color matching |
| Swipe gestures | Too slow, breaks one-thumb play |
| On-screen buttons | Takes screen space, breaks immersion |
| Drag the brick | Doesn't fit the auto-runner feel |

The left/right split: **invisible, ambidextrous, instant**. Works with thumb while holding phone in one hand.

---

## Retention Hooks (Implemented)

- **Best score persistence** — localStorage, shown on start + game over
- **Total coins** — lifetime counter across runs
- **NEW BEST indicator** — celebration on game over when beating record
- **Combo system** — risk/reward (lose it all on crash)
- **Level 2 unlock** — narrative moment at wall 10

## Retention Hooks (Planned — Viral Phase)

- **Milestone celebrations** (walls 5, 10, 25, 50, 100)
- **Personal-best ghost line** — see where your record died
- **Slow-motion replay** — 3-second shareable clip of your best fit
- **Shareable end-card** — "I SURVIVED 47 WALLS" image
- **Daily challenge** — seed-based same wall sequence for everyone
- **Character unlocks** — spend coins on new mascots
- **World themes** — city → beach → space → lava every 10 walls

---

## Failure Design

A good hyper-casual game makes failure **feel fair and funny**, not punishing.

- ✅ **Instant restart** — no ads between deaths
- ✅ **Dramatic fail animations** — crash is a spectacle, not a punishment
- ✅ **"NEW BEST!" celebration** — even failures can be wins
- ❌ **No "Watch ad to continue"** — breaks the loop
- ❌ **No punishment timers** — kills momentum

---

## Appendix: Why Babylon.js?

Tried Three.js first. Works, but:
- Materials feel flat by default
- Post-processing requires stitching example files
- Glow layer is non-trivial

Switched to Babylon.js:
- PBR materials look richer out of the box
- `DefaultRenderingPipeline` gives bloom + FXAA + ACES in 3 lines
- Built-in `GlowLayer` for the wall gap
- Single ~2MB CDN script, no build tools

**Result:** noticeably more polished with less code.

---

## Design Principles

1. Every tap matters
2. Every match feels earned
3. Every death is a story
4. Every run ends with "one more"
