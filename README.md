# Snek

A Snake game in a single self-contained HTML file — no build step, no dependencies, no server required.

**[Play it live →](https://gilangagustian.github.io/snek/)**

---

## Features

- **Canvas rendering** — vanilla JS, zero external dependencies
- **Power-ups** — pulsing diamond pickups that spawn randomly during play:
  - ⚡ **Speed boost** (yellow `!!`) — halves the tick interval for 6 s
  - ✂ **Shrink** (cyan `><`) — instantly cuts the snake's tail in half (minimum length 3)
  - ✦ **2× multiplier** (magenta `2x`) — doubles all points earned for 8 s
- **Combo scoring** — eating food within 2.5 s of the last eat builds a streak (×2 up to ×5), multiplying points per eat; stacks with the 2× power-up for up to ×10 per food
- **Sound effects** — synthesised via Web Audio API (no audio files): blip on eat, two-tone chime on power-up, rising pitch per combo level, descending thud on death
- **Speed scaling** — base tick interval decreases with score, capped at a minimum
- **High score** — persisted in `localStorage`
- **Responsive** — canvas scales to the largest square that fits any screen
- **Mobile controls** — on-screen D-pad (hidden on desktop) plus swipe gestures on the canvas
- **Keyboard** — arrow keys and WASD; Enter or Space to restart
- **PWA-ready** — `apple-mobile-web-app-capable` meta tag and inline web app manifest; add to home screen on iOS and Android

## Controls

| Input | Action |
|---|---|
| Arrow keys / WASD | Steer |
| Enter / Space | Restart after game over |
| On-screen D-pad | Steer (touch devices) |
| Swipe on canvas | Steer (touch devices) |

## Scoring

| Situation | Points |
|---|---|
| Eat food | +1 |
| ×2 combo streak | +2 per food |
| ×5 combo streak | +5 per food |
| 2× multiplier active | doubles the above |
| ×5 combo + 2× multiplier | +10 per food |

## Customising

All tunable constants are at the top of the `<script>` block in `index.html`:

```js
const COLS    = 20;   // grid width
const ROWS    = 20;   // grid height
const BASE_MS = 160;  // starting tick interval (ms)
const MIN_MS  = 65;   // fastest possible tick (ms)
const ACCEL   = 3;    // ms removed per point scored
```

Power-up colours, icons, durations, and spawn probability (`0.28`) are in the `PU` object and `maybeSpawnPu()` function just below.

## Running locally

No server needed — just open `index.html` in any modern browser:

```
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```
