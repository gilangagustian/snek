# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Snek is a Snake game implemented as a **single self-contained HTML file** (`index.html`) with zero dependencies, no build step, and no package manager. The only other file is `sw.js`, a minimal service worker for PWA/offline support.

## Running Locally

```
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```

No server, no install, no build. Open the file directly in a browser.

## Architecture

All game logic lives inside the `<script>` block of `index.html`. There are no modules or separate files.

**Game loop:** `tick()` is called via `setTimeout` rather than `requestAnimationFrame`. The interval is recalculated each tick: `Math.max(MIN_MS, BASE_MS - score * ACCEL)`. When the speed power-up is active, the interval is further halved (`* 0.55`).

**State variables (all module-level):**
- `snake` — array of `{x, y}` grid cells, head at index 0
- `dir` / `queued` — current and buffered direction vectors; `queued` is applied at tick start to prevent 180° reversal
- `food`, `pu`, `bonus` — current positions of food, power-up, and bonus orb (`null` when absent)
- `effects` — `{ speed, curse, multi }` each holding an expiry timestamp (`Date.now()` ms), or falsy when inactive
- `combo`, `lastEat` — combo streak count and timestamp of last food eat
- `flash` — `{ text, color, until }` for mid-canvas transient messages; rendered in `draw()` and faded out

**Power-ups (`PU` object):** Keyed by `'speed'`, `'curse'`, `'multi'`. Each entry holds `color`, `glow`, `icon`, `label`, `ttl`. The `curse` effect inverts the direction vector inside `steer()`.

**Rendering:** `draw()` is a single synchronous canvas pass — grid lines, food, power-up diamond, bonus star, snake segments, flash text, and active-effect HUD tags (written to `fxEl.innerHTML`).

**Input:** `steer(d)` is the single entry point for direction changes. It applies the curse inversion and enforces the 180° guard before writing to `queued`. Keyboard, D-pad buttons, and swipe gestures all call `steer()`.

**PWA:** The manifest is built as a Blob URL at runtime (Chrome rejects `data:` URIs). The icon is drawn via `makeIcon(sz)` onto an offscreen `<canvas>` and exported as a PNG data URL. `sw.js` caches `./` and `./index.html` on install.

**High score:** Persisted to `localStorage` under key `'snek-hi'`.

## Tunable Constants

All at the top of the `<script>` block:

```js
const COLS = 20, ROWS = 20;          // grid dimensions
const BASE_MS = 160, MIN_MS = 65, ACCEL = 3; // speed curve
```

Power-up spawn probability (`0.28`) is in `maybeSpawnPu()`; power-up expiry TTL (9000 ms) is set inline there. Bonus orb spawn probability (`0.22`) and TTL (5000 ms) are in `maybeSpawnBonus()`. Power-up effect durations are in the `PU` object.
