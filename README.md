# Snake

A one-page Snake game served by a small, zero-dependency Node.js server.

- No `npm install` needed — there are no dependencies.
- The entire game (markup, styles, logic) lives in `index.html`.
- Best score persists in the browser via `localStorage`.

## Requirements

- Node.js 18 or newer (tested on v24)

Check your version:

```bash
node --version
```

## Start the server

From the project directory:

```bash
npm start
```

Or run it directly, without npm:

```bash
node server.js
```

You should see:

```
Snake running at http://127.0.0.1:3000
Press Ctrl+C to stop.
```

Now open **http://localhost:3000** in your browser.

Stop the server with `Ctrl+C`.

### Using a different port

Set the `PORT` environment variable:

```bash
PORT=8080 npm start
```

If the default port is taken, the server exits with a message telling you to pick
another one.

### Exposing it on your network

By default the server binds to `127.0.0.1`, so it is reachable only from this
machine. To listen on all interfaces:

```bash
HOST=0.0.0.0 npm start
```

## How to play

| Input | Action |
| --- | --- |
| `↑` `↓` `←` `→` or `W` `A` `S` `D` | Steer the snake |
| `Space` | Pause / resume |
| `R` | Restart |
| Swipe on the board | Steer (touch devices) |
| Tap the board | Start, pause, or restart |

Eat the red food to grow and score. The snake speeds up as your score rises.
Hitting a wall or your own body ends the run. Fill the entire board to win.

## Project layout

```
snake-game/
├── index.html    the whole game: canvas, styles, and game loop
├── server.js     static file server (Node http module only)
├── package.json  defines the `npm start` script
└── README.md
```

## Configuration

Both are read from the environment when the server starts:

| Variable | Default | Purpose |
| --- | --- | --- |
| `PORT` | `3000` | Port to listen on |
| `HOST` | `127.0.0.1` | Interface to bind |

Game tuning constants live at the top of the script block in `index.html`:
`CELLS` (grid size), `BASE_TICK` (starting speed in ms per step), and `MIN_TICK`
(fastest speed reachable).

## Troubleshooting

**`Port 3000 is already in use`** — another process holds the port. Start on a
different one with `PORT=3001 npm start`, or find the culprit:

```bash
lsof -i :3000
```

**Page loads but nothing draws** — the game uses `CanvasRenderingContext2D.roundRect`,
which needs Chrome 99+, Safari 16.4+, or Firefox 112+. Update your browser.

**Changes to `index.html` don't appear** — the server sends `Cache-Control: no-cache`,
so a normal reload is enough; if you still see stale content, hard-reload with
`Cmd+Shift+R` (macOS) or `Ctrl+Shift+R`.
