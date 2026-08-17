# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running

No build, no package manager, no tests. Static files only.

```bash
open index.html              # macOS, works from file://
python3 -m http.server 8000  # or serve statically
```

## Architecture

Three files, no modules — `game.js` is a single script in global scope, loaded by `<script src="game.js">` at the end of `index.html`. It grabs DOM nodes by id at top level and calls `init()` immediately on load.

State lives in module-level `let` bindings (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `dropAccum`, `animId`). `init()` resets all of them and is also the restart handler.

- `board` — `ROWS × COLS` array of ints; `0` empty, `1–7` index into `COLORS` / `PIECES`.
- Pieces are square matrices; rotation is transpose+reverse (`rotateCW`), so `current.shape` is mutated in place rather than tracked as a rotation index. No SRS kick table — `tryRotate` tries x-offsets `[0,-1,1,-2,2]`.
- `collide(shape, x, y)` is the single validity check used by movement, rotation, ghost projection and game-over detection.
- Game loop: `requestAnimationFrame(loop)` accumulates `dt` into `dropAccum`, drops one row when it exceeds `dropInterval`, then redraws everything each frame (no dirty regions).
- Locking chain: `lockPiece()` -> `merge()` -> `clearLines()` -> `spawn()`; `spawn()` calls `endGame()` if the new piece already collides.
- Progression, all in `clearLines()`: `level = floor(lines/10)+1`, `dropInterval = max(100, 1000 - (level-1)*90)`, score `LINE_SCORES[cleared] * level`.

## Gotchas

- Canvas dimensions are hardcoded in `index.html` (`board` 300×600, `next-canvas` 120×120). Changing `COLS`, `ROWS` or `BLOCK` in `game.js` requires updating `width`/`height` to `COLS*BLOCK` × `ROWS*BLOCK`. `drawNext` also assumes a 4×4 preview grid at 30px.
- User-facing strings (overlay, controls list, README) are Spanish; keep new UI text Spanish.
- Pause and game-over share one `#overlay`; `#restart-btn` always calls `init()`, so it restarts rather than resumes.
