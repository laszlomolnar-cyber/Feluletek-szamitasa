# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file, client-side PWA (in Hungarian) for surveying a property's rooms and exporting a Word (`.docx`) measurement report ("Ingatlanfelmérési jegyzőkönyv"). The surveyor enters ceiling height and per-room dimensions; the app computes floor area and paintable surface (walls + ceiling), optionally subtracting door/window openings, then generates a formatted `.docx` in the browser.

There is **no build system, no package manager, and no tests**. The "app" is `index.html` with two vendored libraries.

## Running / developing

- Open `index.html` directly in a browser, or serve the folder over HTTP (e.g. `python3 -m http.server`) so the PWA manifest, install prompt, and relative `<script>` tags behave correctly.
- No install, compile, lint, or test step exists. Editing `index.html` and reloading is the entire dev loop.
- Deployed as static files (the app is designed to run as an installed PWA / offline).

## Files

- `index.html` — the entire application: inline CSS, inline JS, all four screens, and Word export logic.
- `docx.js` — vendored [docx](https://www.npmjs.com/package/docx) v9.7.1 IIFE build (exposes `window.docx`). Bundled locally so export works offline; `ensureDocx()` tries CDN first, then falls back to this file (loaded via `<script>` at the bottom of `index.html`).
- `qrcode.js` — vendored QR library (exposes `window.QRCode`), used to share the app URL offline.
- `lotzi-splash.svg` — splash screen shown ~3s on startup.

## Architecture (all inside `index.html`)

**State.** A single in-memory `state` object (`{ mode, propType, propId, ceiling, rooms[] }`) is the source of truth. Rooms hold `{id, name, w, l, h, note, doors[], windows[]}`. **Nothing is persisted except `surveyorName` in `localStorage`** — reloading loses the survey.

**Screens.** Four `<section>` elements (`screen-mode`, `screen-prop`, `screen-rooms`, `screen-summary`) toggled by `show(id)`, which hides all and reveals one. Flow: pick mode → property type/ID + ceiling → add rooms → summary + export.

**Two calculation modes** (`state.mode`):
- `'full'` — paintable surface = walls + ceiling, no subtraction.
- `'subtract'` — also subtracts door/window areas. The opening-entry UI (draft rows, confirm, per-room door/window lists) is only active in this mode.

**`roomCalc(r)` is the single source of calculation truth.** Both the live room cards (`renderRooms`) and the Word export (`exportDocx`) call it. Per-room height falls back to `state.ceiling` when blank. If you change a formula, change it here.

**Word export (`exportDocx`).** Builds the document programmatically with the `docx` library — a styled title, metadata block, a rooms table (header + striped body rows + totals row), per-room notes, and a summary. Colors are hex constants mirroring the CSS theme (`INK`, `HEAD`, `ROW`, `TOT`). `Packer.toBlob` → anchor download. After a successful export it fires a no-cors "stats ping" (just the total m²) to a Google Apps Script endpoint.

## Conventions to preserve

- **Hungarian everywhere** — UI strings, comments, variable names mix Hungarian and English. New user-facing text must be Hungarian.
- **Decimal comma.** Users type `2,70`. `num()` converts comma→dot for parsing; `fmt()`/`dim()` format back to comma for display and for the `.docx`. Always round-trip numbers through these helpers rather than using `parseFloat`/`toFixed` directly.
- **Vanilla JS only.** No framework, no bundler, no npm. DOM access via the `$ = id => document.getElementById(id)` helper and inline `onclick=` handlers. Keep new code in this style.
- Escape any user-supplied string rendered as HTML with `esc()` (used for room names/notes).
