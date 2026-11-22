# CLAUDE.md

## What this is

A single, self-contained HTML slide deck for a talk: **"Under the Hood of Agentic AI — An Executive Primer"** (INSEAD Alumni Association Switzerland, 24 June 2026). Author: Sam Bourton.

The entire project is one file: [INSEAD-talk-v2.html](INSEAD-talk-v2.html) (~2,500 lines). No build system, no dependencies, no package manager, not a git repo. The only external resources are Google Fonts (Inter, JetBrains Mono) loaded via CDN.

## Running / previewing

Open the file directly in a browser — no server needed:

```sh
open "INSEAD-talk-v2.html"
```

Slides scroll vertically with CSS scroll-snap. Navigation: right-edge dot rail, a left section-jump sidebar (appears after the title slide), and a PDF export button (appears on content/closing slides).

## File anatomy

The single file has four distinct regions, in order:

1. **`<style>`** (~lines 10–1429) — all CSS. Design tokens live in `:root` as CSS variables (colors like `--coral`, `--purple`; fonts `--mono`, `--sans`; the `--accent-gradient`). Responsive breakpoints at `max-width: 1100px` and `700px`. A `@media print` block (~line 1274) drives PDF export layout.
2. **`<script type="application/json" id="slide-data">`** (~lines 1434–1673) — a structured JSON content model of the talk (`meta` + `slides[]`). **See the gotcha below.**
3. **Body HTML** (~lines 1676–2400) — the actual rendered slides: `<section class="slide" data-slide="...">` elements, plus the title slide, closing slide, appendix divider, and the section-jump nav.
4. **`<script>`** (~lines 2406–2531) — runtime behavior: builds the nav dots, IntersectionObserver-based active-section tracking and `.reveal` animations, and the PDF export modal (`openExportModal` / `runPrint`, which toggles per-slide checkboxes and calls `window.print()`).

## Important gotcha: the JSON is NOT rendered

The `slide-data` JSON block is **not consumed by any JavaScript** — the runtime `<script>` works entirely off the hand-authored DOM (`.slide` sections), not the JSON. The two are out of sync (the JSON describes ~18 slides; the DOM has ~14 sections). Treat the JSON as a separate authoring/source-of-truth artifact.

When editing slide **content or appearance**, edit the body HTML `<section>` elements — that is what actually displays. Decide deliberately whether to also update the JSON to keep it as a clean content reference; do not assume editing one updates the other.

## Editing conventions

- Each content slide is `<section class="slide" data-slide="<id>">` with a `.slide-number`, a `.slide-label`, an `<h1>`/heading (bold via `**...**` in JSON, `<em>`/`<strong>` in HTML), and a `.slide-sub`. The `data-slide` id must match its entry in the section-jump nav (`data-target` / `scrollToSlide('<id>')`).
- Use existing CSS variables for any colors/spacing rather than hardcoding new values — the deck has a deliberate, consistent palette.
- Elements with class `reveal` (and `reveal-delay-N`) fade in on scroll via IntersectionObserver; reuse these classes for new animated content.
- Adding a slide means: add the `<section>` in the body, add a matching `.section-jump-item` to the nav, and (optionally) mirror it in the JSON.
