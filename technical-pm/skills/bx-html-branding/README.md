# bx-html-branding

The `bx-pptx` house style translated to the web. Produces **single-file,
self-contained HTML documents** for professional communication — decision memos,
briefings, reports, summaries, and one-pagers — that are readable on screen and
print cleanly to PDF. Same warm pastel palette on charcoal type; retuned from
"projection and density" to "a calm reading column with clear hierarchy".

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
├── README.md         ← this file
├── SKILL.md          ← Design Ideas (house style) + build/QA workflow
├── house-style.css   ← design tokens (CSS custom properties) + components
└── template.html     ← starter standalone document
```

## Usage

1. Copy `template.html`.
2. Inline the full `house-style.css` into its `<style>` block.
3. Pick `.doc` (memos/reports) or `.doc--wide` (one-pagers); fill in content with
   the house class vocabulary.
4. Open in a browser, screenshot, and run the QA bug-hunt in `SKILL.md`.

Reference the CSS custom properties (`--color-charcoal`, `--color-coral`, …) —
never hardcode hex values in the document body.
