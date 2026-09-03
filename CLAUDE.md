# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris implementation. No build system, no dependencies, no package.json. Three files do everything: `index.html`, `style.css`, `game.js`.

## Running

Open `index.html` directly, or serve statically:

```bash
python3 -m http.server 8000
```

There are no build, lint, or test commands.

## Architecture

All game logic lives in `game.js` (~300 lines, single global scope, no modules). Key structures:

- **Board model**: `ROWS × COLS` matrix; each cell holds `0` (empty) or a color index `1–7` identifying the locked piece.
- **Pieces**: square matrices. Rotation is transpose + row-reverse (`rotateCW`).
- **`tryRotate`**: implements basic wall kicks by attempting ±1 and ±2 column offsets before rejecting a rotation.
- **`collide`**: single source of truth for movement validity (bounds + occupied cells).
- **Game loop**: `requestAnimationFrame`-driven; accumulates dt and drops the piece when `dropInterval` is exceeded. Level scales speed via `max(100, 1000 − (level − 1) × 90)` ms.
- **Rendering**: two `<canvas>` elements — `#board` (main play field) and `#next` (preview). Ghost piece is drawn with `globalAlpha = 0.2`.
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` × level. Hard drop adds 2/cell, soft drop 1/row.

### Coupling to watch

`COLS`, `ROWS`, and `BLOCK` in `game.js` must stay consistent with the `width`/`height` attributes of `<canvas id="board">` in `index.html` (`width = COLS × BLOCK`, `height = ROWS × BLOCK`). Changing one without the other breaks rendering.

The README (Spanish) is the authoritative design doc for gameplay behavior.
