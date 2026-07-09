# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running

No build, deps, or tests. Open `index.html` directly (`open index.html`) or serve statically
(`python3 -m http.server 8000`). Reload the browser to see changes.

## Architecture

Vanilla JS Tetris — 3 cooperating files, no modules/bundler:

- `index.html` — DOM + two canvases: `#board` (300×600) and `#next-canvas` (preview).
- `style.css` — dark arcade theme; `.overlay.hidden` toggles pause / game-over screens.
- `game.js` — all game logic in module-global mutable state (`board`, `current`, `next`,
  `score`, `level`, etc.), driven by `requestAnimationFrame`.

Key concepts in `game.js`:

- Board = `ROWS×COLS` matrix; each cell is `0` (empty) or a color index `1–7` (also the
  piece type). `COLORS` and `PIECES` are 1-indexed with a leading `null`.
- Pieces are square matrices; `rotateCW` transposes + reverses; `tryRotate` applies basic
  wall kicks (`[0,-1,1,-2,2]`).
- `collide(shape, x, y)` is the single source of truth for bounds + overlap; reused by
  movement, rotation, `ghostY`, soft/hard drop, and spawn game-over check.
- `loop(ts)` accumulates `dt` into `dropAccum` and steps the piece down every `dropInterval` ms.
- `lockPiece` → `merge` → `clearLines` → `spawn`; `spawn` triggers `endGame()` if the new
  piece collides immediately.
- `init()` (re)starts the game and is bound to the restart button; called on load.

## Gotchas

- Board pixel size is hardcoded in `index.html` (`width="300" height="600"`). It must equal
  `COLS*BLOCK × ROWS*BLOCK` from `game.js` — change both together.
- Tunable constants live at the top of `game.js`: `COLS`, `ROWS`, `BLOCK`, `COLORS`,
  `LINE_SCORES`, and the `dropInterval` formula in `clearLines`.
- README is in Spanish and is the detailed reference for mechanics/controls.
