# technical-pm

Composable, on-demand product-management skills. Each is usable on its own and works from whatever input you provide — there is no required order and no orchestrating subagent. Together they cover the path from raw stakeholder input to exec-ready strategy.

## The skills

| Skill | Output folder | Output |
|-------|--------------|--------|
| `technical-pm-capture-demand` | `User_Demand/` | Structured demand note from meeting transcripts, demo notes, or stakeholder emails |
| `technical-pm-surface-themes` | `Themes_Evidence/` | Theme cluster doc — each theme cites ≥2 evidence sources |
| `technical-pm-write-prd` | `PRD_Decisions/` | Functional PRD with decisions, alternatives rejected, and constraints named |
| `technical-pm-technical-design` | `TechnicalDesign/` | Candidate approaches weighed on trade-offs with a recommended path, plus the chosen architecture, data flow, integrations, NFRs, and risks |
| `technical-pm-translate-to-workitems` | `Delivery_WorkItems/` | Epic → Feature → Story → Task draft Markdown files |
| `technical-pm-exec-narrative` | `Prioritization_Strategy/` | One-page exec summary; optional PowerPoint deck |

## Additional skills

- **`bx-ppt`** — cross-platform PowerPoint generation via `pptxgenjs`.
- **`bx-html-branding`** — standalone HTML documents in the house style (memos, briefings, one-pagers).
- **`sdd-generator`** — Solution Design Document from a PRD or work items.
- **`docx`** — create, read, and edit Word documents (`.docx`): TOCs, headings, tracked changes, find-and-replace, images.
- **`pdf`** — read, create, and manipulate PDFs: extract text/tables, merge/split, fill forms, watermark, OCR.
- **`xlsx`** — create, read, and edit spreadsheets (`.xlsx`/`.xlsm`/`.csv`): formulas, formatting, charts, data cleanup.

## Conventions

- Skills run on demand and independently. Invoke whichever one the user asks for — there is no required sequence.
- Each skill works from whatever input the user provides: pasted text, a file, or a folder they point at. It uses an upstream artifact only when the user supplies one; it never requires a prior skill to have run, and it never stops to route the user elsewhere.
- Output folders organize artifacts; they do not gate anything. Write to the conventional folder if present, otherwise ask where to save.
- Every theme cites ≥2 evidence sources. Every PRD decision names the alternative rejected and why. Every technical recommendation traces to a decision or requirement. Don't fabricate upstream material — flag `[TBD]` and ask.
- Output is the artifact, not commentary about producing it. Markdown tables and structured bullets — no filler prose.
