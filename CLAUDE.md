# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No dependencies, no `package.json`, no build, no tests, no linter. The README and UI text are in Spanish.

## Running

Open `index.html` directly, or serve statically (e.g. `python -m http.server 8000`, `npx serve .`) and visit `http://localhost:8000`.

## Architecture

Three files: `index.html` (DOM + canvases), `style.css`, and `game.js` (all logic, one script loaded with `'use strict'`, global state, no modules).

- **State** lives in module-level `let` variables declared on one line in `game.js` (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`, ...) and is reset by `init()`. The restart button just calls `init()`.
- **Board** is a `ROWS × COLS` matrix of `0` or a color/piece index 1–8. `COLORS` and `PIECES` are indexed by that same number (index 0 is `null`), so piece type, shape values and color all share one index.
- **Game loop**: `loop(ts)` via `requestAnimationFrame` accumulates `dropAccum` and gravity-drops one row when it exceeds `dropInterval`; it also calls `draw()` every frame. Pause/game-over cancel the frame via `animId`; unpausing restarts `loop` manually.
- **Piece lifecycle**: `lockPiece()` → `merge()` → `clearLines()` → `spawn()`. `spawn()` promotes `next` to `current`, and calls `endGame()` if the new piece collides immediately.
- **Input**: single `keydown` handler (uses `e.code`); hard drop, soft drop, rotate (with wall kicks `[0,-1,1,-2,2]` in `tryRotate`) all go through `collide()`.
- **Rendering**: `draw()` renders grid, board, ghost piece (`ghostY()`, alpha 0.2), then current piece on `#board`; `drawNext()` renders the preview on `#next-canvas`.

## Gotchas

- Changing `COLS`, `ROWS` or `BLOCK` requires updating the `width`/`height` attributes of `<canvas id="board">` in `index.html` (`COLS*BLOCK` × `ROWS*BLOCK`); the next-piece canvas is a fixed 120×120 (4 cells × 30px, hardcoded `NB = 30` in `drawNext`).
- HUD values come from `updateHUD()`; call it after any change to `score`/`lines`/`level`.
- Score/speed: `LINE_SCORES[cleared] * level`; soft drop +1/row, hard drop +2/row; `dropInterval = max(100, 1000 - (level-1)*90)`; level up every 10 lines.
