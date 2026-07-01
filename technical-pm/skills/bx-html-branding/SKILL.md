---
name: bx-html-branding
description: |
  Creates readable, easy-to-digest standalone HTML documents in the house
  style — a warm pastel aesthetic with a coral/caramel/sand/tea-green/beige
  palette on charcoal text, three role-based font families (serif headings, sans
  body, mono labels), a vertical caramel accent bar on headings, tinted callouts,
  a semantic color-state system (recommended / caution / rejected), and generous
  white space. Optimized for professional communication: decision memos,
  briefings, reports, summaries, one-pagers, and technical design docs. Output is
  a single self-contained .html file that opens in any browser and prints cleanly
  to PDF. Use when the user asks to "write a memo", "draft a briefing", "make a
  one-pager", "write a technical design doc", "turn this into a readable HTML
  doc", or wants a professional document in our house style as HTML.
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
| **Start a memo / briefing / report / one-pager** | Copy `template.html`, inline `house-style.css` into its `<style>` block, fill in content |
| **Start a technical design doc** | Copy `template-technical-design.html`, inline `house-style.css`, fill in the cover + problem sections |
| Choose a width | `.doc` for memos/briefings/reports; `.doc--wide` for one-pagers; the technical-design type is full-bleed (`<body class="techdoc">`) |
| Apply the palette/components | Use the class vocabulary below — do not hand-roll new colors |
| Verify the result | Open in a browser and screenshot; run the QA bug-hunt below |

**Two document families, one stylesheet.** The memo family (`.doc`) is a single
centered reading card — memos, briefings, reports, one-pagers. The
technical-design family (`.techdoc`) is a full-bleed, sectioned layout with a
richer motif kit (cover, TOC, numbered problem sections, approach cards,
decision record, data-flow rail) for docs shaped as *problems → approaches →
trade-offs → recommendation → architecture*. Both share the same tokens,
typography, and warm palette.

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

### Semantic color states (the meaning behind the colors)

The same warm palette also does **wayfinding**: color is mapped to *state*, so a
reader can tell recommended from rejected before reading a word. This is what
makes comparison-heavy docs (technical designs especially) scannable. The state
tokens are derived tints/darks of the existing hues — no new colors, just
readable partners (a deeper tea for edges, a paler coral for backgrounds).

| State / meaning | Token family | Hue | Where it appears |
|-----------------|--------------|-----|------------------|
| **Recommended / pro / pass / low risk** | `--state-good-*` | Tea green | `.approach.rec` ring+badge, `.pc.pro` `+`, `.col-rec` table column, `.recommend`, `.step.guard`, `.sev.low` |
| **Caution / con / medium risk** | `--state-warn-*` | Caramel | `.pc.con` `−`, `.advisory`, `.step.fail`, `.sev.med`, index chips |
| **Rejected / disqualified / high risk** | `--state-bad-*` | Coral | `.killed` tag, `td.bad` cell, `.sev.high` |
| **Settled / structural / neutral** | `--state-neutral-bg`, charcoal, sand | Beige / charcoal | `.pnum`, decision-record labels, `.pri-pill.lead` |

- **Memo family keeps the "one sharp accent" discipline:** coral appears exactly
  once in a memo/briefing/one-pager. The multi-state system is for the
  `.techdoc` type, where coral legitimately recurs as the *rejected/critical*
  signal across cards, cells, and severity pills.
- Reach for a state class instead of `.accent` whenever you're marking a
  winner, a loser, or a severity — let the color mean something.

### Typography — three families, fixed roles

Hierarchy is carried by **type family**, not just size and weight. A serif
heading over a sans body reads as a heading before you register its size; mono
on labels and indices signals "system / technical artifact." Contrast comes from
the family contrast, freeing color to do other work.

| Role | Font (token) | Used for | Treatment |
|------|------|----------|-----------|
| **Titles & headings** | Serif — `--font-serif` (Iowan Old Style → Palatino → Georgia) | `h1`/`h2`/`h3`, `.doc-title`, cover `h1`, approach names, the `.pick` line | Title fluid `clamp(2rem, …, 2.75rem)`; h2 fluid; `text-wrap: balance`; tight letter-spacing |
| **Body & UI** | Sans — `--font-sans` (Segoe UI → system) | Body copy, list items, `.priorities h4`, table body | Body 1rem at 1.65 line-height; lead 1.1875rem |
| **Labels & indices** | Mono — `--font-mono` (SF Mono → Consolas → system) | Eyebrows, `.doc-meta`/`.meta` keys, captions, `.pnum`/index chips, pills, `code` | Uppercase, tracked (`0.08–0.16em`), small |

- All three are **system-font stacks** — no web fonts, so the file stays
  self-contained and renders everywhere.
- Body copy is left-aligned and held to a ~42rem (~70ch) measure for
  readability. In `.techdoc`, prose sits in `.col` (the measure) inside the wide
  `.wrap` canvas, so tables/cards/rails can break out full-width.
- Numbers in tables/stats use `.tnum` (`tabular-nums`) so columns align.
- Inline emphasis: **bold charcoal** (`<strong>`), or `.label` (bold + sand
  underline) for category names in lists. Use `.accent` (coral) for inline
  emphasis only when it is the document's single sharp accent (memo family).

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

### Technical Design Kit (`.techdoc` only — opt-in)

Extra motifs for the technical-design type. Start from
`template-technical-design.html`, which wires them up in order. These live below
the "TECHNICAL DESIGN KIT" divider in `house-style.css` and never touch the memo
classes above.

1. **Full-bleed layout** — `<body class="techdoc">` with full-width `.cover` /
   `.band` / `<section>` siblings; a `.wrap` (60rem canvas) centers content and
   `.col` holds prose to the reading measure so grids/tables break out wide.
2. **Cover masthead** (`.cover`) — an inverted charcoal band with a caramel top
   rule, serif `h1`, `.sub` deck, and a `.meta` metadata grid (Date/Owner/…).
   One of at most **two** dark anchors per doc.
3. **Eyebrow with tick-rule** (`.eyebrow`) — mono, tracked, with a short caramel
   rule; the section kicker throughout.
4. **Decided-inputs block** — `.grid-2` pairing a `.deflist` with a
   `.priorities` tie-breaker panel (`.chain` of `.pri-pill`s).
5. **Advisory** (`.advisory`) — the headline tension / scope assumption, in the
   caution (caramel) state with a `§` mark.
6. **Contents index** (`.toc`) — two-column numbered nav.
7. **Constraints panel** (`.constraints`) — the second inverted charcoal anchor;
   binding constraints with `§` markers.
8. **Problem sections** (`.problem` + `.problem-head` + `.pnum` + `.stake`) — a
   numbered "case file" header (the chip replaces the caramel bar) and an
   "At stake:" framing line.
9. **Approach cards** (`.approaches` + `.approach`) — one per option with a
   `.badge`, `.pc.pro`/`.pc.con` markers, and a `.reco-tag` (green) or `.killed`
   (coral) tag. Mark the winner `class="approach rec"` for the green ring.
10. **Comparison table** (`.table-scroll` + `th.dim`/`.col-rec`/`td.bad`) —
    row-label column, a green-highlighted recommended column, coral bad cells.
11. **Recommendation** (`.recommend` + `.pick` + `.pin`) — the chosen option in
    the green state with a letter pin.
12. **Data-flow rail** (`.flow` + `.step` + `.rail`/`.dot`/`.line`) — numbered
    dots with `.gate`/`.guard`/`.fail` states and a `.flow-key` legend. Use this
    instead of an ASCII `<pre>` diagram.
13. **Process list** (`ol.steps`) — decimal-leading-zero mono index chips.
14. **Severity pills** (`.sev.high/.med/.low`) and **question cards**
    (`.questions` + `.q` + `.qtag` for TBD/Resolved).

### Layout Patterns

- **Decision memo** — eyebrow + title + `.doc-meta` block, then `.lead`, then
  short sections, closing with a `.callout--key` recommendation.
- **Briefing** — eyebrow + title, `.lead` summary, 2-4 sections with callouts for
  context, no memo metadata.
- **Report / summary** — title, `.lead`, multiple `h2` sections, tables and
  callouts interleaved, optional `hr` dividers between major parts.
- **One-pager** — `.doc--wide`, a `.stat-grid` near the top, tight sections, one
  coral accent. Designed to print to a single page.
- **Technical design doc** — `.techdoc`: `.cover` masthead → decided-inputs
  `.band` (deflist + priorities + advisory + TOC) → context + `.constraints` →
  one `#p*` `.problem` section per decision (approach cards → comparison table →
  recommendation) → decision-record table → architecture (`.flow` rail +
  components) → NFRs / risks (`.sev`) / open questions (`.q`). Answer-first still
  applies: each problem leads with the recommendation.

### Spacing & Reading

- Hold body to the ~42rem measure; one-pagers may widen to `.doc--wide` (56rem);
  `.techdoc` prose stays in `.col` while the `.wrap` canvas runs to 60rem.
- Generous vertical rhythm between sections; the premium feel comes from
  restraint, not density.
- Print styles strip the card chrome, avoid breaking inside callouts/tables, and
  keep headings with their content — so browser "Print → Save as PDF" is clean.

### Avoid (Common Mistakes)

- **NEVER underline a heading** — the caramel bar to its left is the house
  treatment. Underlines beneath headings read as AI-generated.
- **Coral discipline depends on the family.** In the **memo family**, coral
  appears once per document (one `.accent`, or one `.callout--key`, or one
  `.stat--accent`). In `.techdoc`, coral is the *rejected/critical* state and may
  recur — but only ever to mean that; never decoratively.
- **Don't oversaturate fills** — tint backgrounds stay in the tea-green / beige /
  sand / pale-state range. Coral and caramel are for accents and edges, never
  large light fields. The **only** saturated fills are the charcoal anchors
  (`.cover`, `.constraints`) — at most two per doc.
- **Don't hardcode colors** — use the CSS custom properties from
  `house-style.css` (palette *or* `--state-*` tokens).
- **Don't link the stylesheet externally** — inline it so the file is portable.
- **Don't center body text** — left-align all paragraphs and lists.
- **Keep fonts to the three role stacks** — serif headings, sans body, mono
  labels. Don't introduce a fourth family or swap roles (e.g. sans headings).
- **Don't run body text full-bleed** — keep prose in the measure (`.col` in
  `.techdoc`); only grids, tables, cards, and the flow rail use the full canvas.
- **Don't reach for an ASCII `<pre>` diagram** — use the `.flow` rail.
- **Don't color without meaning** — in `.techdoc`, a green/caramel/coral element
  should signal recommended/caution/rejected, not decoration.
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
- Any heading with an underline (forbidden — should be the left caramel bar, or
  the `.pnum` chip in a `.problem-head`)
- **Memo family:** coral used on more than one element. **`.techdoc`:** coral
  used for anything *other than* rejected/critical (it may recur, but only in
  that meaning); a green/caramel/coral element that doesn't signal a state
- Oversaturated tint fields, or more than two charcoal anchors per doc
- Centered body text; prose running full-bleed instead of held to the measure
- A font outside the three role stacks, or the wrong role (e.g. a sans heading, a
  serif body, a non-mono eyebrow/label)
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
├── SKILL.md                        ← this file — house style + workflow
├── README.md                       ← catalog entry
├── house-style.css                 ← design tokens + components (inline into output)
├── template.html                   ← starter memo / briefing / report / one-pager
└── template-technical-design.html  ← starter technical design doc (.techdoc kit)
```

## Dependencies

None required. Output is plain HTML + CSS that renders in any modern browser. A
headless browser (e.g. Chrome/Chromium) is helpful for screenshot-based visual
QA and for "Print → Save as PDF", but is not required to produce the document.
