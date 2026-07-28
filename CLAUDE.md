# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm start                # serve on http://127.0.0.1:3000
node server.js           # same, without npm
PORT=8080 npm start      # different port
HOST=0.0.0.0 npm start   # expose on the local network
```

There is no build step, no dependencies (`npm install` is unnecessary), no linter, and no test suite. Verification is manual: start the server and play the game in a browser. Requires Node 18+.

## Architecture

Two files matter:

- `index.html` — the entire game: markup, CSS, and an IIFE containing the game loop. Nothing is imported or bundled.
- `server.js` — a static file server built on Node's `http` module only. It maps URL paths to files under `__dirname`, rejects anything resolving outside it, serves `index.html` at `/`, and sends `Cache-Control: no-cache` so edits show up on a plain reload.

### Game loop and state

The game is a state machine over `state ∈ {'ready', 'playing', 'paused', 'over'}`. Every state change should also update the overlay via `showOverlay`/`hideOverlay` — the overlay's visibility is the only UI expression of `state`.

`loop()` runs on `requestAnimationFrame` and always draws, but only advances simulation when `state === 'playing'`. Stepping uses a fixed-timestep accumulator (`acc`) against `tickInterval()`, which shrinks as score rises (`BASE_TICK - score * 2`, floored at `MIN_TICK`). Consequently the number of `step()` calls per frame varies, and the inner `while` re-checks `state` because `step()` can end the game mid-frame.

### Conventions worth preserving

- **Direction buffering**: input writes `nextDir`; `step()` promotes it to `dir`. Setting `dir` directly from an input handler would let two keypresses inside one tick reverse the snake into itself. `setDirection` rejects reversals and no-ops.
- **Colors live in CSS**: `draw()` reads `--snake`, `--food`, etc. through `getVar()` at paint time. Restyle by editing the `:root` custom properties, not by hardcoding colors in JS.
- **`food === null` means the board is full**, which is the win condition — `placeFood()` enumerates free cells and yields `null` when there are none. Guard food reads accordingly.
- **Self-collision excludes the last segment**, since the tail vacates its cell on the same tick.
- Tuning constants (`CELLS`, `BASE_TICK`, `MIN_TICK`) sit at the top of the script block. `CELL` is derived from `canvas.width / CELLS`, so the canvas must stay square and its `width`/`height` attributes divisible by `CELLS`.

### Browser requirement

Drawing depends on `CanvasRenderingContext2D.roundRect` (Chrome 99+, Safari 16.4+, Firefox 112+). Best score persists in `localStorage` under the key `snake.best`.
