# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Vanilla JavaScript Tetris game using HTML5 Canvas. No dependencies, no build tools, no package manager — three files: `index.html`, `style.css`, `game.js`.

UI text is in Spanish.

## Running

Open `index.html` directly in a browser, or serve with any static server:

```bash
python3 -m http.server 8000
```

There are no build, lint, or test commands.

## Architecture

All game logic lives in `game.js` (~300 lines) as module-level functions sharing global state (`board`, `current`, `next`, `score`, `lines`, `level`, etc.).

- **Board**: `ROWS×COLS` (20×10) 2D array where 0 = empty, 1–7 = piece color index
- **Pieces**: defined in `PIECES` as small 2D matrices; index matches `COLORS` array
- **Game loop**: `requestAnimationFrame`-based; accumulates delta time and drops the piece when `dropAccum >= dropInterval`
- **Rendering**: two `<canvas>` elements — main board (300×600px) and next-piece preview (120×120px); `BLOCK` constant (30px) ties grid coordinates to pixel coordinates
- **Collision**: `collide(shape, ox, oy)` checks bounds and overlap against the board
- **Rotation**: clockwise transpose+reverse in `rotateCW`, with horizontal wall kicks (±1, ±2) in `tryRotate`

## Key Constraints

- If `COLS`, `ROWS`, or `BLOCK` change, the `<canvas>` `width`/`height` attributes in `index.html` must be updated to match (`COLS*BLOCK` × `ROWS*BLOCK`).
- Scoring uses `LINE_SCORES[cleared] * level`; hard drop adds 2 pts/cell, soft drop 1 pt/row.
- Speed formula: `max(100, 1000 - (level-1) * 90)` ms per drop; level increments every 10 lines.
