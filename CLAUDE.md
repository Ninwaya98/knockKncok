# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

This is a single-file interactive gift website (`knock-knock.html`) — no build system, no dependencies, no server needed. Open directly in any browser.

## Architecture

Everything lives in one file with three sections:

**CSS (inline `<style>`)** — All layout, character drawing, and animations. The character is built entirely from CSS shapes (no images or SVG). Key classes:
- `.character-wrap` — positions the character inside `.doorway` using `translateX`. States: `gone`, `peek`, `out`, `walking`, `waving`, plus mood modifiers `char-angry`, `char-smirk`.
- `.door` — the clickable door, animated with CSS 3D `perspective + rotateY`. States: `open`, `open-fast`, `crack`, `close-fast`.
- `.controls` — fixed-height container below the door so button/hint changes never cause layout shift.
- `--scale` CSS variable on `.scene` drives responsive scaling via JS.

**HTML (body)** — Static structure only. The `.doorway` contains the character; the `.door` sits on top (z-index 10). The speech `#bubble` is absolutely positioned inside `.door-frame` so it escapes the doorway's `overflow:hidden`.

**JS (inline `<script>`)** — State machine driving the experience:
- `visitCount` tracks which conversation branch to use (0→1→2, capped at convos array length).
- `locked = true` after visit 3; knocking only shakes + shows status text.
- `rapidCount` / `trackRapid()` — detects 4+ rapid clicks and triggers an angry pop-out interrupt, independent of visit state.
- All async sequences use `async/await` + a `sleep(ms)` helper. Never use `setTimeout` directly for sequencing.
- Sound: Web Audio API only. Three functions — `playKnock()` (3-layer: noise burst + sine thump + transient click), `playCreak()` (sawtooth sweep + noise texture), `playSlam()` (heavy thud + crack burst).

## Conversation flow

`convos` array — index 0 = first visit (8 lines), index 1 = second visit (annoyed, 3 lines), index 2 = third visit (eyes-only crack, 2 lines). After index 2, `locked = true`.

Line `action` values control character position mid-conversation: `peek`, `peek-angry`, `walk-out`, `smirk`, `leave`, `slam`, `eyes`, `eyes-slam`.

## Deployment

- **GitHub repo:** https://github.com/Ninwaya98/knockKncok
- **Hosted on Netlify** — auto-deploys when changes are pushed to `main`
- To deploy: just `git add`, `git commit`, `git push origin main`

## Key constraints

- Single `.html` file — keep everything inline, no external JS/CSS files.
- No images — all graphics are CSS shapes.
- Google Fonts (`Pacifico` + `Nunito`) are loaded via `<link>` — requires internet for fonts to render correctly; fallbacks are `cursive` / `sans-serif`.
