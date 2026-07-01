# bx-html-branding

The `bx-pptx` house style translated to the web. Produces **single-file,
self-contained HTML documents** for professional communication — decision memos,
briefings, reports, summaries, one-pagers, and **technical design docs** — that
are readable on screen and print cleanly to PDF. Same warm pastel palette on
charcoal type; retuned from "projection and density" to "a calm reading column
with clear hierarchy".

Two document families share one stylesheet:

- **Memo family** (`<main class="doc">`) — a centered reading card for memos,
  briefings, reports, one-pagers.
- **Technical design** (`<body class="techdoc">`) — a full-bleed, sectioned
  layout with a richer motif kit (cover, TOC, numbered problem sections, approach
  cards, decision record, data-flow rail) for *problems → approaches →
  recommendation → architecture* docs.

Typography carries hierarchy through **three role-based font families** — serif
headings, sans body, mono labels/indices (all system-font stacks). Color does
double duty as a **semantic state system**: tea-green = recommended, caramel =
caution, coral = rejected/critical — so comparison-heavy docs are scannable at a
glance.

## What it produces

A single `.html` file with its CSS inlined — no external dependencies, safe to
email, share, or print. Open it in any browser; use the browser's
"Print → Save as PDF" for a clean PDF.

## Color Palette

| Name | Hex | Role |
|------|-----|------|
| Charcoal | `2B2B2B` | All body copy and headings (the only dark; carries contrast) |
| Vibrant Coral | `FE5F55` | Single-use sharp accent per document |
| Light Caramel | `F0B67F` | Heading accent bar, callout edges, link underlines, list markers |
| Sand Dune | `D6D1B1` | Rules, dividers, table header borders, tag fills |
| Tea Green | `C7EFCF` | Callout backgrounds, stat cards |
| Beige | `EEF5DB` | Alternate fill, key-callout background |

## How it differs from `bx-pptx`

| Area | `bx-pptx` | `bx-html-branding` |
|------|-----------|--------------------|
| Output | `.pptx` deck (pptxgenjs) | Single self-contained `.html` file |
| Optimized for | Projection, slide density | Reading, scannability, clean PDF print |
| Accent bar | Left of the slide title | Left of every `h2` heading (`::before`) |
| Tints | Card fills, rails, icon circles | Callouts, stat cards |
| Icons | Required line-art in tint circles | Not required — text-first documents are fine |
| QA | Slide-to-image subagent inspection | Browser screenshot + print-preview inspection |

## Files

```
bx-html-branding/
├── README.md                       ← this file
├── SKILL.md                        ← Design Ideas (house style) + build/QA workflow
├── house-style.css                 ← design tokens (CSS custom properties) + components
├── template.html                   ← starter memo / briefing / report / one-pager
└── template-technical-design.html  ← starter technical design doc (.techdoc kit)
```

## Usage

1. Copy the template that matches the document: `template.html` for a
   memo/briefing/report/one-pager, or `template-technical-design.html` for a
   technical design doc.
2. Inline the full `house-style.css` into its `<style>` block.
3. Fill in content with the house class vocabulary — `.doc`/`.doc--wide` for the
   memo family, or the `.techdoc` kit (cover, problem sections, approach cards,
   flow rail) for a design doc.
4. Open in a browser, screenshot, and run the QA bug-hunt in `SKILL.md`.

Reference the CSS custom properties (`--color-charcoal`, `--color-coral`, …) —
never hardcode hex values in the document body.
