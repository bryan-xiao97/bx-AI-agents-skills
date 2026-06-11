---
name: bx-html-branding
description: |
  Creates readable, easy-to-digest standalone HTML documents in the house
  style — a warm pastel aesthetic with a coral/caramel/sand/tea-green/beige
  palette on charcoal text, Segoe UI typography, a vertical caramel accent bar
  on headings, tinted callouts, and generous white space. Optimized for
  professional communication: decision memos, briefings, reports, summaries,
  and one-pagers. Output is a single self-contained .html file that opens in any
  browser and prints cleanly to PDF. Use when the user asks to "write a memo",
  "draft a briefing", "make a one-pager", "turn this into a readable HTML doc",
  or wants a professional document in our house style as HTML.
---

# bx-html-branding Skill

Produce single-file, self-contained HTML documents in the house style — the
same warm pastel identity as the `bx-pptx` decks, retuned for reading rather
than presenting. Where the deck style optimizes for projection and density, this
optimizes for a calm reading column, clear hierarchy, and clean print/PDF
output. Every document is one `.html` file with its CSS inlined, so it can be
emailed, shared, or printed without external dependencies.

## Quick Reference

| Task | How |
|------|-----|
| **Start a new document (default)** | Copy `template.html`, inline `house-style.css` into its `<style>` block, fill in content |
| Choose a width | `.doc` for memos/briefings/reports; `.doc--wide` for one-pagers |
| Apply the palette/components | Use the class vocabulary below — do not hand-roll new colors |
| Verify the result | Open in a browser and screenshot; run the QA bug-hunt below |

---

## Building a Document (Default Path)

1. Copy `template.html` to the output location.
2. **Inline the full contents of `house-style.css`** into the `<style>` block. The
   document must carry its own styles — never `<link>` to an external file. A
   shared `.html` that loses its CSS is worthless.
3. Pick the document shell: keep `.doc` for memos and reports; switch to
   `.doc--wide` for dense one-pagers.
4. Write content using the house structure (answer first, then support) and the
   class vocabulary below.
5. Run the QA bug-hunt before declaring done.

**Write the prose to the project's communication standards:** answer first, lead
with the recommendation, no filler. The styling makes it readable; the structure
makes it scannable.

---

## Design Ideas — House Style

This is the deck house style translated to the web. Same palette, same
typography intent, same "one sharp accent" discipline. The vertical caramel
accent bar moves from the left of slide titles to the left of section headings.

### House Style Overview

- **Tone:** Warm, approachable, editorial. Charcoal type on white with soft
  pastel tints for structure. Generous white space, a constrained reading column.
- **60-30-10 split:** ~60% white reading surface, ~30% charcoal for type and
  structure, ~10% tea-green/beige tints with caramel/coral accents.
- **One sharp accent per document:** Coral (`#FE5F55`) appears exactly once — the
  single recommendation, the one number that matters, the key callout. If
  everything is accented, nothing is.
- **Contrast comes from type, not dark fills:** The palette has no dark color.
  Charcoal body copy and the lone coral accent carry all contrast. Tints
  (tea-green, beige, sand) are for structure, never bold full-strength fields.

### Color Palette

| Role | Name | Hex | Usage |
|------|------|-----|-------|
| Primary text | Charcoal | `2B2B2B` | All body copy, headings, the only dark — carries contrast |
| Sharp accent | Vibrant Coral | `FE5F55` | Single-use standout — the recommendation, the hero stat, the key callout edge |
| Structural accent | Light Caramel | `F0B67F` | Recurring accents — the vertical heading bar, callout edges, link underlines, list markers |
| Muted neutral | Sand Dune | `D6D1B1` | Rules, dividers, table header borders, tag fills |
| Primary tint | Tea Green | `C7EFCF` | Callout backgrounds, stat cards |
| Light tint | Beige | `EEF5DB` | Alternate callout/stat fill, the key-callout background |
| Neutral | White / Charcoal | `FFFFFF` / `2B2B2B` | Reading surface and body text |

All seven are defined as CSS custom properties in `house-style.css`
(`--color-charcoal`, `--color-coral`, etc.). Reference the tokens — never
hardcode a hex in the document body.

### Typography

| Role | Font | Treatment |
|------|------|-----------|
| Major (title, headings) | **Segoe UI Semibold** (`font-weight: 600`) | Title 2rem; h2 1.5rem; h3 1.1875rem; tight letter-spacing |
| Minor (body, meta) | **Segoe UI** | Body 1rem at 1.65 line-height; lead 1.1875rem; meta/footer 0.8125rem |

- Font stack leads with `"Segoe UI"` (matches the house identity and the Outlook
  context) then falls back to the native system stack.
- Body copy is left-aligned and held to a ~42rem (~70ch) measure for readability.
- Inline emphasis: **bold charcoal** (`<strong>`), or `.label` (bold +
  sand underline) for category names in lists. Use `.accent` (coral) for inline
  emphasis only when it is the document's single sharp accent.

### Component Vocabulary (use these, don't invent)

1. **Heading accent bar** — every `h2` renders a thin caramel bar to its LEFT via
   `::before`. This is the web analog of the deck's vertical title bar and the
   house alternative to the forbidden underline-under-heading.
2. **Lead paragraph** (`.lead`) — one larger opening paragraph that answers first.
3. **Memo metadata** (`.doc-meta`) — To / From / Date / Re block, ruled top and
   bottom. Drop it for reports and one-pagers.
4. **Callouts** (`.callout`, `.callout--beige`, `.callout--key`) — tinted blocks
   with a caramel left edge for supporting context, quotes, or examples.
   `.callout--key` swaps the edge to coral for the single takeaway.
5. **Stat cards** (`.stat-grid` + `.stat`) — for one-pagers; alternate tea-green
   and beige fills. `.stat--accent` promotes one value to coral.
6. **Tables** — clean, zebra-striped on the faint page wash, sand header rule, no
   heavy borders.
7. **Tags / pills** (`.tag`, `.tag--tea`, `.tag--accent`) — status and category
   labels.
8. **Footer** (`.doc-footer`) — confidential mark (only if requested) on the
   left, author/date on the right.

### Layout Patterns

- **Decision memo** — eyebrow + title + `.doc-meta` block, then `.lead`, then
  short sections, closing with a `.callout--key` recommendation.
- **Briefing** — eyebrow + title, `.lead` summary, 2-4 sections with callouts for
  context, no memo metadata.
- **Report / summary** — title, `.lead`, multiple `h2` sections, tables and
  callouts interleaved, optional `hr` dividers between major parts.
- **One-pager** — `.doc--wide`, a `.stat-grid` near the top, tight sections, one
  coral accent. Designed to print to a single page.

### Spacing & Reading

- Hold body to the ~42rem measure; one-pagers may widen to `.doc--wide` (56rem).
- Generous vertical rhythm between sections; the premium feel comes from
  restraint, not density.
- Print styles strip the card chrome, avoid breaking inside callouts/tables, and
  keep headings with their content — so browser "Print → Save as PDF" is clean.

### Avoid (Common Mistakes)

- **NEVER underline a heading** — the caramel bar to its left is the house
  treatment. Underlines beneath headings read as AI-generated.
- **Don't overuse the coral accent** — one element per document maximum (one
  `.accent`, or one `.callout--key`, or one `.stat--accent`, not several).
- **Don't oversaturate fills** — backgrounds stay in the tea-green / beige /
  sand range. Coral and caramel are for accents and edges, never large fields.
- **Don't hardcode colors** — use the CSS custom properties from
  `house-style.css`.
- **Don't link the stylesheet externally** — inline it so the file is portable.
- **Don't center body text** — left-align all paragraphs and lists.
- **Don't mix font families** — the Segoe UI stack only.
- **Don't run body text full-bleed** — keep the constrained measure; wide
  unbroken lines kill readability.
- **Don't use low-contrast text** — charcoal on white or on any tint is fine;
  never pale caramel/sand text on white, or pale-on-pale.
- **Don't bury the answer** — lead with the recommendation or headline, then
  support it.

---

## QA (Required)

**Assume there are problems. Your job is to find them.** Your first render is
rarely correct.

### Content QA

- Read the rendered text top to bottom: is the answer first? Any leftover
  `[bracketed placeholders]` from the template? Typos, wrong order?
- Grep for unfilled placeholders before declaring success:

```bash
grep -nE "\[[^]]+\]|lorem|ipsum" output.html
```

If grep returns template brackets or filler, fix them.

### Visual QA

Open the file in a browser and capture it (a headless screenshot works), then
inspect — ideally with fresh eyes via a subagent, since you've been staring at
the markup and will see what you expect.

```bash
# Example with a headless browser; any screenshot tool works
# (skip if no browser is available and inspect by reading the rendered HTML)
```

Inspect for:

General issues
- Text overflow, awkward wrapping, or lines running too wide (measure lost)
- Callouts/tables breaking badly across a page boundary in print preview
- Uneven spacing — cramped in one place, a void in another
- Low-contrast text (pale-on-pale, sand/caramel text on white)
- Tables with misaligned columns or missing header rules

House-style violations
- Any heading with an underline (forbidden — should be the left caramel bar)
- Coral used on more than one element in the whole document
- Oversaturated tint fields instead of restrained tints
- Centered body text
- A font other than the Segoe UI stack
- Externally linked CSS instead of inlined

### Verification Loop

1. Render → screenshot/read → inspect
2. List issues found (if none, look harder)
3. Fix
4. Re-check the affected areas — one fix often shifts spacing elsewhere
5. Repeat until a full pass is clean

**Do not declare success until you've completed at least one fix-and-verify
cycle, including a print-preview check if the document is meant to be a PDF.**

---

## Files

```
bx-html-branding/
├── SKILL.md          ← this file — house style + workflow
├── README.md         ← catalog entry
├── house-style.css   ← design tokens + components (inline into output)
└── template.html     ← starter standalone document
```

## Dependencies

None required. Output is plain HTML + CSS that renders in any modern browser. A
headless browser (e.g. Chrome/Chromium) is helpful for screenshot-based visual
QA and for "Print → Save as PDF", but is not required to produce the document.
