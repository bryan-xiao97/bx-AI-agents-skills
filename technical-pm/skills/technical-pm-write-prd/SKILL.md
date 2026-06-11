---
name: technical-pm-write-prd
description: >
  Turns stage-2 themes into a functional Product Requirements Document (PRD) — problem statement, audience, scope (functional requirements and out-of-scope items), decisions with alternatives rejected, constraints, and success signals. Use whenever the user says "write a PRD", "document the decisions", "write up the requirements", "create the product spec", "formalize this into a PRD", or is ready to move from themes to formal requirements. The PRD is purely functional — the technical design lives in a separate stage-4 document (see /technical-pm-technical-design). Writes to the 3_PRD_Decisions/ folder.
---

# technical-pm-write-prd

Produces the stage-3 functional PRD. Every decision traces to a theme; every theme is cited by name. This document is purely functional — the technical design lives in a separate stage-4 document (see `/technical-pm-technical-design`).

## Workflow

### Step 1 — Confirm upstream exists

Check for a `2_Themes_Evidence/` document for this product. If none exists, stop and route the user to `/technical-pm-surface-themes` first. If multiple themes docs exist in `2_Themes_Evidence/`, read the most recent one (by date in the filename or file modification time). If a themes doc exists but is sparse, proceed with `[TBD]` placeholders and flag the gaps clearly.

### Step 2 — Derive the decision points

From the themes doc, extract the open product choices the themes force — the questions this PRD must answer.

- Every entry in the themes doc's "Cross-cutting tensions" section automatically becomes a decision point. A tension between two themes is resolved by a decision; it cannot be ignored or deferred silently.
- Carry each theme's "Open questions" into the relevant decision point. If no decision point covers an open question, it carries forward into the PRD's Open questions section.
- Write each decision point as: a short title, one sentence stating what is at stake, and which theme(s) force it.

The output of this step is a named list of decision points — not answers yet, just the choices that must be made.

### Step 3 — Decide with the user

**Hard checkpoint — do not draft the PRD until every decision point is resolved or explicitly parked.**

For each decision point, present:
- 2–3 genuine candidate positions — real options someone could defend, not strawmen built to lose
- A recommendation with rationale that cites the themes doc by name

Wait for the user to pick an option, correct it, or substitute their own position. Record the outcome as the resolved decision. Any options the user did not pick are the "alternatives rejected" — they must be drawn from the options actually presented here, never invented after the fact.

If the user parks a decision point as unresolved, carry it into the PRD's Open questions section.

### Step 4 — Draft the product requirements

Write the following. Cite themes by name throughout.

- **Problem statement:** What user need or business gap does this address? Cite the driving themes.
- **Audience:** Who uses this? Distinguish primary (direct users) from secondary (beneficiaries or affected parties).
- **Scope:** Two parts.
  - *In scope* — a numbered functional-requirements list (`FR-1`, `FR-2`, …). Each requirement is one line describing a concrete capability. Each traces to the resolved decision or theme that generated it. This list is what stage 5 decomposes into work items — write capabilities, not aspirations.
  - *Out of scope* — an explicit bullet list of what this product does not do. Unresolved decisions that were parked go here until decided.
- **Decisions:** Each resolved decision point from step 3, formatted as one subsection. Record the decision taken, the alternatives rejected (drawn from step 3 — not invented here), and the rationale citing the theme. One subsection per decision.
- **Constraints:** Known limits (business, regulatory, operational). Mark unknown constraints `[TBD]`.
- **Success signals:** Observable outcomes that indicate this worked. Behavioral, not just metrics.

### Step 5 — Review with user

Present the full draft. Ask the user to:
- Confirm or revise decisions — especially the "alternatives rejected" entries
- Confirm the scope boundary: are the FR list and out-of-scope items correct?
- Fill in any `[TBD]` items they already know
- Add missing constraints or success signals

### Step 6 — Write the file

Write to `3_PRD_Decisions/`. Filename: `{Product} - PRD - {MM.DD}.md`. Report the full path on completion.

When the PRD is written, point the user to `/technical-pm-technical-design` as the next stage — the build-ready technical design that turns these functional decisions into architecture.

---

## Output format

````markdown
# PRD — {Product} — {YYYY-MM-DD}

_Themes sourced from: [`2_Themes_Evidence/{themes-filename}`]({relative path})_

---

## Problem statement

{What user need or business gap this addresses. Cite the themes driving it by name.}

## Audience

**Primary:** {Who directly uses this}
**Secondary:** {Who benefits or is affected}

## Scope

### In scope

1. **FR-1** — {Capability, one line} _(Decision: {Decision title} or Theme: {Theme name})_
2. **FR-2** — {Capability, one line} _(Decision: {Decision title} or Theme: {Theme name})_
3. **FR-3** — {Capability, one line} _(Decision: {Decision title} or Theme: {Theme name})_

### Out of scope

- {Capability or use case explicitly excluded}
- {Parked decision point that was not resolved}

---

## Decisions

### Decision 1: {Short title}

| | |
|---|---|
| **Decision** | {What was decided} |
| **Alternatives rejected** | {What else was considered and why it was ruled out} |
| **Rationale** | {Why this decision was made — cite the theme} |
| **Theme** | {Theme name from stage-2} |

### Decision 2: {Short title}

...

---

## Constraints

- {Constraint}
- [TBD] {Constraint requiring investigation}

## Success signals

- {Observable outcome that indicates success}
- {Another observable outcome}

---

## Open questions

- [TBD] {Unresolved product-level question}
````
