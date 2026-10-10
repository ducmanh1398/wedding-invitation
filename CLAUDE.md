# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Vietnamese wedding invitation (thiệp mời cưới) for Hương Ly & Đức Mạnh, 14.11.2026, Capella Gallery Hall. Each family side is a folder served as a clean URL: `nha-gai/index.html` → `<domain>/nha-gai` (bride's side, "nhà gái"); a future `nha-trai/index.html` → `<domain>/nha-trai` (groom's side). Each page is one self-contained HTML file: inline CSS, inline JS, and every asset embedded as a base64 data URI. Only Google Fonts and the Google Maps iframe and links are external.

Guest names: `?to=Tên` is shown on the cover and sent as the name with the RSVP.

No build, package manager, lint, or tests. Preview with `npx serve .` (or open the file directly).

## Working with the 1.4 MB file

A few lines are huge base64 blobs (100–730 KB each). Never read or print the whole file or those lines, and never let Edit/Write rewrite them. Read with line ranges, and truncate long lines when grepping (e.g. `cut -c1-200`). Known blob lines (the numbers shift after edits):
- `CONFIG.music` (mp3, the biggest). `musicStart`/`musicEnd`/`musicVolume` sit on the same line, after the blob.
- `FLOWER` map (`1`, `3`, `calla`, `plaque`), used by `<img data-flower="…">` and `#plaqueBg`.
- `ENV_ART` (`envelope`, `seal`): the hero envelope and the wax seals.
- The bank-transfer QR `<img>`s in the gift modal, and the calendar heart background in CSS (`.grid .hit::before`).

## Structure

Layout: a mobile-first column with a max width of 480px (`.app`). The `--we` CSS variable drives the hero and envelope sizing. Colors and fonts are tokens on `:root` (`--wine`, `--navy`, `--gold`, `--script`, `--names`…).

Page flow: cover (`#cover`, which removes itself when the guest taps open and starts the music) → hero envelope → info → photo slider → calendar → venue/map → RSVP. Floating action buttons at the bottom (RSVP, wishes modal `#mWish`, gift modal `#mGift`, like). Content is hard-coded Vietnamese text in the HTML.

`<script>` at the bottom, in this order:
- `CONFIG` block ("edit here"): photo slots (`hero`, `slide1..3`; an empty slot shows an SVG placeholder through `.ph.empty`), calendar (`startOffset` is the number of blank cells before day 1, with weeks starting Monday; `highlight` lists the wedding day), music.
- Feature blocks are IIFEs or plain handlers, each marked by a `/* ==== … ==== */` comment. The `$` helper is `document.getElementById`.
- Music: plays the `CONFIG.music` file with fade and an optional loop segment. If it is empty, a WebAudio chime is generated instead.
- Everything is client-side only. RSVP, wishes, and likes are not sent or stored anywhere, so they reset on reload.
- An ink-metrics alignment routine centers the "&" between the two names in `.cv-names`, `.plaque-txt`, and `.names2`. Keep that three-child structure if you edit those names.

The code comments are in Vietnamese. Match that when adding comments.
