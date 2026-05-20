# product-manager

End-to-end product management across a 5-stage lifecycle. Each stage produces a structured artifact that the next stage consumes.

## The 5 stages

| Stage | Skill | Output folder | Output |
|-------|-------|--------------|--------|
| 1 — Capture demand | `pm-capture-demand` | `1_UserDemand_TechnicalReviews/` | Structured demand note from meeting transcripts, demo notes, or stakeholder emails |
| 2 — Surface themes | `pm-surface-themes` | `2_Themes_Evidence/` | Theme cluster doc — each theme cites ≥2 demand sources |
| 3 — Author PRD | `pm-write-prd` | `3_PRD_Decisions/` | Combined product + technical design spec with decisions, alternatives rejected, constraints |
| 4 — Translate to work items | `pm-translate-to-workitems` | `4_Delivery_WorkItems/` | Epic → Feature → Story → Task draft Markdown files |
| 5 — Exec narrative | `pm-exec-narrative` | `5_Prioritization_Strategy/` | One-page exec summary; optional PowerPoint deck |

## Additional skills

- **`bx-ppt`** — cross-platform PowerPoint generation via `pptxgenjs`.
- **`bx-ppt-COM`** — Windows-only variant driving PowerPoint via COM automation for richer fidelity.
- **`sdd-generator`** — Solution Design Document from a PRD or work items.

## Subagent

- **`product-manager`** — orchestrates the full 5-stage flow. Never skips a stage. Use when the user wants to move an idea end-to-end rather than invoking one stage at a time.

## Conventions

- Stages are sequential. Don't write a PRD without themes; don't translate to work items without a PRD.
- Every theme cites ≥2 evidence sources from stage 1.
- Every PRD decision names the alternative rejected and why.
- Output is the artifact, not commentary about producing it. Markdown tables and structured bullets — no filler prose.
