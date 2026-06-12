# bx-pptx

A house-style extension of the base `pptx` skill. Same generation / edit / QA scripts; layered on top is everything needed to produce decks in the house style — a warm pastel palette on charcoal type. New decks are built from scratch with `pptxgenjs`; the editing workflow applies when the user supplies their own deck or template.

## What this skill adds over the base `pptx` skill

| Area | Base `pptx` skill | `bx-pptx` |
|------|-------------------|-----------|
| Workflow scripts | `unpack.py`, `pack.py`, `add_slide.py`, `clean.py`, `thumbnail.py`, `soffice.py`, `validate.py` | **Same** — reused unchanged |
| House style guidance | None | **`SKILL.md` "Design Ideas" section** — palette, typography, motifs, layout patterns, do/don't list |
| Default build path | From scratch | **From scratch with the house style applied** (`pptxgenjs.md` + Design Ideas) |
| Layout discovery | Read XML by hand | **`scripts/list_layouts.py`** — prints master + layouts for any user-supplied `.pptx` |
| QA prompt | Generic | House-style checks (vertical caramel accent bar, no title underline, line-art icons, footer presence, coral-accent-once rule) |

## Color Palette

| Name | Hex | Role |
|------|-----|------|
| Charcoal | `2B2B2B` | Titles, headers, body text (the only dark; carries all contrast) |
| Vibrant Coral | `FE5F55` | Single-use sharp accent per slide |
| Light Caramel | `F0B67F` | Recurring structural accent (vertical title bar, sub-accent) |
| Sand Dune | `D6D1B1` | Muted neutral — dividers, footnote bars, mid chart series |
| Tea Green | `C7EFCF` | Primary tint — left rails, card backgrounds, icon circles |
| Beige | `EEF5DB` | Light tint — alternate card fill, soft page-background tint |

## Files unique to `bx-pptx`

```
bx-ppt/
├── README.md                  ← this file
├── SKILL.md                   ← Design Ideas + workflow (house-style sections only)
├── editing.md                 ← edit workflow for user-supplied decks
├── pptxgenjs.md               ← from-scratch generation reference
└── scripts/
    └── list_layouts.py        ← runtime layout introspection for user-supplied templates
```

Everything else under `scripts/` (including `scripts/office/`) is inherited from the base `pptx` skill, untouched.

## Recent changes

- Replaced the navy financial-services identity with a warm pastel house style (coral / caramel / sand / tea-green / beige on charcoal text).
- Removed the bundled master template; from-scratch with `pptxgenjs` is now the default build path.
- `scripts/list_layouts.py` now requires an explicit template path (no bundled default), for use only when a user supplies their own template.

No edits to the base `pptx` workflow scripts (`unpack.py`, `pack.py`, `add_slide.py`, `clean.py`, `thumbnail.py`, `soffice.py`, `validate.py`).
