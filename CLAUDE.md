# Linda's Card Games

Mobile card games built as single HTML files — playable in any mobile browser, no install required.

## Project goal
Fun games for Linda's phone. Keep everything self-contained in one `.html` file per game.

## Current games

### `solitaire.html` — Klondike Solitaire
Classic solitaire with green felt, red/black cards, drag-and-drop, double-tap to auto-move, score, timer, and a win celebration (cards fly off the foundations with physics).

**Live at:** https://iplay2.tv/solitaire.html

## Deployment
Files are served by nginx on an AWS EC2 instance.
- Web root: `/var/www/html/`
- Domain: `iplay2.tv`
- Deploy: upload the `.html` file to `/var/www/html/` via the `aws-ec2` MCP connector

The MCP connector (`mcp__aws-ec2__*`) is configured and working — use `upload_file` and `run_command` directly.

## Architecture decisions
- **Single HTML file per game** — CSS, JS, and markup all inline. No build step, no dependencies.
- **CSS variables for card sizing** — `--cw` and `--ch` drive all card dimensions off viewport width so layout never breaks.
- **Cards use `position: absolute`** inside `.col` divs with JS-set column heights.
- **No `overflow: hidden` on the tableau** — use `overflow-x: hidden; overflow-y: auto` instead, or cards get clipped.
- **`window.addEventListener('load', newGame)`** — not `DOMContentLoaded`, to ensure layout is computed before card dimensions are read.

## Solitaire game internals
- State: `stock`, `waste`, `fnd[4]`, `tab[7]` — plain JS arrays of `{s, r, up}` card objects
- `render()` redraws everything from state each time
- `doMove()` → `snap()` (undo history) → mutate state → `render()` → `checkWin()`
- Drag: `pointerdown` on card → build `.ghost` div → `pointermove` moves ghost → `pointerup` calls `dropTarget()` hit test
- Double-tap detection in `onPD()` via `lastTap` timestamp
- Win celebration: `celebrateCards()` launches all 52 cards with bounce physics via `requestAnimationFrame`; overlay fades in after 3s

## What's next (ideas)
- Spider Solitaire
- FreeCell
- High score persistence (localStorage)
- Sound effects
