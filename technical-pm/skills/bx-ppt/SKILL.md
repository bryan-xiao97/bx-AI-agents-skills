---
name: bx-pptx
description: |
  Creates, reads, edits, and designs PowerPoint presentations (.pptx) in the house
  style — a warm pastel aesthetic with a coral/caramel/sand/tea-green/beige palette
  on charcoal text, Segoe UI typography, line-art icons, and generous white space.
  Includes a visual QA workflow with slide-to-image conversion.
  Use when the user asks to "create a deck", "make a presentation",
  "build a pitch deck in our house style", or wants a new presentation that matches
  the house style.
---

# bx-pptx Skill

Create PowerPoint presentations in the house style. This skill carries the same generation/edit/QA workflow as the base pptx skill, but the **Design Ideas** section encodes a specific palette, typography, motifs, and layout patterns. New decks are built from scratch with pptxgenjs by default; the editing workflow applies when the user supplies their own deck or template.

## Quick Reference

| Task | Guide |
|------|-------|
| Read/analyze content | `python -m markitdown presentation.pptx` |
| **Create a deck (default)** | **Read `pptxgenjs.md`, apply the house style below** |
| Edit an existing user-supplied deck | Read `editing.md` |
| List layouts in a user-supplied template | `python "<skill>/scripts/list_layouts.py" path.pptx` |

---

## Reading Content

```bash
# Text extraction
python -m markitdown presentation.pptx

# Visual overview (paths relative to skill folder — see Path Convention)
python scripts/thumbnail.py presentation.pptx

# Raw XML
python scripts/office/unpack.py presentation.pptx unpacked/
```

---

## Creating from Scratch (Default)

**Read `pptxgenjs.md` for full details.**

This is the default path for a new deck. Build slides with pptxgenjs and apply the **Design Ideas** house style below — palette, typography, motifs, and layout patterns.

---

## Editing Workflow

**Read `editing.md` for full details.** Use this when the user supplies their own deck or template to modify.

1. Start from the user-supplied `.pptx` (skill-relative paths — see Path Convention).
2. Analyze it with `scripts/thumbnail.py`, and list its layouts with `scripts/list_layouts.py path.pptx`.
3. Unpack → manipulate slides → edit content → clean → pack. All scripts live under `scripts/` (skill-relative).

---

## Design Ideas — House Style

This skill produces decks in the house style. Apply these patterns by default unless the user explicitly asks for a different look.

### House Style Overview

- **Tone:** Warm, approachable, editorial. Clean and restrained, charcoal-led type on soft pastel tints. Generous white space.
- **60-30-10 split:** ~60% white background + tea-green/beige tints, ~30% charcoal for type and headers, ~10% caramel/coral for accents.
- **One sharp accent per slide:** Use coral sparingly — only the final step of a process, the standout chart bar, or the single number you want noticed.
- **Light/light contrast through type:** Backgrounds stay light throughout. This palette has no dark color, so contrast comes from charcoal type and the coral accent — never from a dark fill. Use tea-green or sand-dune tinted fields (not solid color) for the title cover and section dividers.

### Color Palette

| Role | Name | Hex | Usage |
|------|------|-----|-------|
| Primary text | Charcoal | `2B2B2B` | Slide titles, key headers, body text (the only dark; carries all contrast) |
| Sharp accent | Vibrant Coral | `FE5F55` | Single-use standout per slide — hero stat, final process step, the one chart bar that matters |
| Structural accent | Light Caramel | `F0B67F` | Recurring accents — vertical title bar, sub-accent, secondary chart series |
| Muted neutral | Sand Dune | `D6D1B1` | Dividers, footnote bars, mid chart series, restrained fills |
| Primary tint | Tea Green | `C7EFCF` | Left-rail callouts, card backgrounds, icon circles |
| Light tint | Beige | `EEF5DB` | Alternate card fill (paired with tea green in 2×2 grids), soft page-background tint |
| Neutral | White / Charcoal | `FFFFFF` / `2B2B2B` | Backgrounds and body text |

### Typography

| Role | Font | Sizes |
|------|------|-------|
| Major (titles, headers) | **Segoe UI Semibold** | Title slide 36pt; content slide title 32pt; section header 12-14pt |
| Minor (body, captions) | **Segoe UI** | Body 10.5-11pt; callout 12pt; footer 9pt; stat number 18pt bold |

- Titles are **left-aligned, bottom-anchored**, spanning ~91% of slide width.
- Body is left-aligned. Only quadrant-card titles may center.
- Inline emphasis: **bold charcoal**, or **bold + underline** for category names within lists. Use coral for inline emphasis only when it is the slide's single sharp accent.

### Visual Motifs (use repeatedly)

1. **Vertical accent bar (left of title)** — thin caramel (~3-4pt) bar to the LEFT of every content slide's title block. This is the house alternative to the forbidden underline-under-title.
2. **Line-art icons in tint circles** — outlined (not filled) icons inside tea-green circles. Use for process steps, quadrant cards, section markers.
3. **Tea-green left rail** — narrow tinted column hosting takeaway stats or an intro paragraph; main narrative breathes on the white right side.
4. **Numbered timeline with segmented under-bar** — N circles in a row, each above a thick horizontal bar segment. Segment colors progress through the palette: tea green → beige → sand dune → caramel → coral (coral reserved for the final/standout segment).
5. **Tinted quadrant cards** — alternating tea-green and beige fills, icon centered above each card's top edge.
6. **Underlined bold inline emphasis** — for category names within lists (e.g., "**Valuation Benchmarking Intelligence**: …").
7. **Footer** — page number bottom-right in charcoal on every content slide. Add a confidential mark (e.g., "Private and Confidential | [page #]") only when the user requests one.

### Layout Patterns

- **Title Slide** — Large white field; charcoal title in caps anchored mid-left; caramel subtitle below; asymmetric arc/curve shapes in the bottom-right corner in tea-green/coral; date bottom-left.
- **Section Divider** — Title-only on a tea-green or sand-dune tinted background; vertical accent bar optional.
- **One-Content (with rail)** — Narrow tea-green left column for headline + supporting stats or intro; wide white right column for the main narrative.
- **Two-Content** — Two equal columns; either side may host text, chart, or visual; icons optional above each.
- **Three Column** — Three equal columns; row labels on the left optional; columns hold text, lists, charts, or visual blocks interchangeably.
- **Four-Content Grid** — 2×2 tinted cards (alternating tea-green and beige), icon floating above each card's top edge, header centered, body left-aligned.
- **Process / Timeline** — N numbered steps across; line-art icons in tea-green circles above each step; thick segmented horizontal bar below in progressing palette colors.
- **Content with Callout (Right or Left)** — Main content on one side; tinted callout block(s) on the other for quotes, examples, or supporting detail.
- **Chart + Commentary** — Numbered list or bullets on one side; single chart or visual on the other; optional footnote bar at bottom.

### Charts

- Bar fill colors progress through the palette: tea green → beige → sand dune → caramel. Reserve coral (`FE5F55`) for the standout bar only.
- Pie wedges follow the same progression; keep wedge order consistent across paired pies.
- Data labels above bars in charcoal, bold.
- Light gridlines (`D6D1B1`), no chart border, no shadow on chart titles.
- Axis text and labels: Segoe UI 9pt, charcoal.

### Spacing

- 0.5" minimum slide margins
- 0.3-0.5" between content blocks
- Title block sits ~0.55" from top, bottom-anchored on a ~0.46" tall placeholder
- Generous white space — the premium feel comes from restraint, not density

### Avoid (Common Mistakes)

- **NEVER use accent lines under titles** — use the vertical accent bar to the LEFT instead. Underlines beneath titles are a hallmark of AI-generated slides.
- **Don't use filled-shape icons** — house style is outlined line-art only.
- **Don't oversaturate fills** — backgrounds stay in the tea-green / beige / sand-dune tint range, never bold full-strength color. Coral and caramel are for accents, not fills.
- **Don't overuse the coral accent** — one element per slide maximum. If everything's accented, nothing is.
- **Don't center body text** — left-align paragraphs and lists; only quadrant-card titles center.
- **Don't skip the footer** — every content slide needs a page number (plus a confidential mark if requested).
- **Don't mix font families** — Segoe UI Semibold + Segoe UI is the only pairing.
- **Don't create text-only slides** — every slide needs an icon, chart, callout, or shape.
- **Don't forget text box padding** — when aligning lines or shapes with text edges, set `margin: 0` on the text box or offset the shape to account for padding.
- **Don't use low-contrast elements** — charcoal on any tint is fine; never light caramel/sand text on white, pale text on a pale tint, or charcoal on coral for body copy.
- **Don't repeat the same layout** — vary across the deck; the layout list above gives nine options.

---

## QA (Required)

**Assume there are problems. Your job is to find them.**

Your first render is almost never correct. Approach QA as a bug hunt, not a confirmation step. If you found zero issues on first inspection, you weren't looking hard enough.

### Content QA

```bash
python -m markitdown output.pptx
```

Check for missing content, typos, wrong order.

**When using templates, check for leftover placeholder text:**

```bash
python -m markitdown output.pptx | grep -iE "xxxx|lorem|ipsum|this.*(page|slide).*layout"
```

If grep returns results, fix them before declaring success.

### Visual QA

**USE SUBAGENTS** — even for 2-3 slides. You've been staring at the code and will see what you expect, not what's there. Subagents have fresh eyes.

Convert slides to images (see [Converting to Images](#converting-to-images)), then use this prompt:

```
Visually inspect these slides. Assume there are issues — find them.

Look for general issues:
- Overlapping elements (text through shapes, lines through words, stacked elements)
- Text overflow or cut off at edges/box boundaries
- Decorative lines positioned for single-line text but title wrapped to two lines
- Source citations or footers colliding with content above
- Elements too close (< 0.3" gaps) or cards/sections nearly touching
- Uneven gaps (large empty area in one place, cramped in another)
- Insufficient margin from slide edges (< 0.5")
- Columns or similar elements not aligned consistently
- Low-contrast text (e.g., light gray text on cream-colored background)
- Low-contrast icons (e.g., dark icons on dark backgrounds without a contrasting circle)
- Text boxes too narrow causing excessive wrapping
- Leftover placeholder content

Look for house-style violations:
- Missing vertical caramel accent bar to the left of the title
- Underline beneath the title (forbidden — should be the vertical bar instead)
- Filled-shape icons (should be outlined line-art only)
- Missing footer (page number bottom-right; confidential mark only if requested)
- Coral accent (`FE5F55`) used on more than one element per slide
- Centered body text (only quadrant-card titles may center)
- Font other than Segoe UI / Segoe UI Semibold
- Oversaturated fills (backgrounds should stay in tea-green / beige / sand-dune tint range)
- Low-contrast text (pale caramel/sand on white, or any pale-on-pale)

For each slide, list issues or areas of concern, even if minor.

Read and analyze these images:
1. .qa/slide-01.jpg (Expected: [brief description])
2. .qa/slide-02.jpg (Expected: [brief description])

Report ALL issues found, including minor ones.
```

### Verification Loop

1. Generate slides → Convert to images → Inspect
2. **List issues found** (if none found, look again more critically)
3. Fix issues
4. **Re-verify affected slides** — one fix often creates another problem
5. Repeat until a full pass reveals no new issues

**Do not declare success until you've completed at least one fix-and-verify cycle.**

---

## Converting to Images

Convert presentations to individual slide images for visual inspection:

```bash
# scripts/ path is skill-relative (see Path Convention)
python scripts/office/soffice.py --headless --convert-to pdf --outdir working/ output/[filename].pptx
mkdir -p .qa
pdftoppm -jpeg -r 150 working/[filename].pdf .qa/slide
```

This creates `.qa/slide-01.jpg`, `.qa/slide-02.jpg`, etc. The `.qa/` directory keeps verification images out of the user's output. The intermediate PDF stays in `working/` so it never triggers a file-changed event.

To re-render specific slides after fixes:

```bash
pdftoppm -jpeg -r 150 -f N -l N working/[filename].pdf .qa/slide-fixed
```

After QA is complete, clean up intermediate files:

```bash
rm -rf .qa working/[filename].pdf
```

---

## Dependencies

All dependencies are pre-installed in the Docker image. No installation needed.

- `markitdown[pptx]` - text extraction (pip, pre-installed)
- `Pillow` - thumbnail grids (pip, pre-installed)
- `pptxgenjs` - creating from scratch (npm, pre-installed globally)
- `react`, `react-dom`, `react-icons` - icon generation (npm, pre-installed globally)
- `cairosvg` - SVG to PNG rasterization (pip, pre-installed)
- LibreOffice (`soffice`) - PDF conversion (auto-configured for sandboxed environments via `scripts/office/soffice.py`)
- Poppler (`pdftoppm`) - PDF to images
