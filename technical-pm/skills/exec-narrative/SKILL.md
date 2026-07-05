---
name: exec-narrative
description: >
  Produces an executive-facing strategy narrative from PRD decisions and a work-item plan — or whatever source material the user provides. Use whenever the user says "exec brief", "strategy narrative", "prioritization doc", "make this executive", "one-pager for leadership", "what do I tell the partners", "what's the strategy", or needs a one-page summary to share with leadership or stakeholders. Can render in Working, Team, or Executive register. Optionally produces a PowerPoint deck. Writes to the doc/Prioritization_Strategy/ folder.
---

# exec-narrative

Produces an exec-facing one-pager that traces from decisions to sequenced delivery. Decision-first. Source-anchored.

## Workflow

### Step 1 — Gather the source material

Work from whatever the user provides. Useful inputs, if available:
- Decisions, constraints, and success signals — from a PRD (e.g. a `doc/PRD_Decisions/` file), pasted notes, or a direct description
- A delivery plan — work item scope and phasing (e.g. a `doc/Delivery_WorkItems/_index.md` file)
- Technical risks worth surfacing to leadership — from a technical design, if one exists

If the core material (decisions or a delivery plan) is missing, ask the user for it or proceed with what is available and flag the gap. Do not invent decisions or phasing.

### Step 2 — Identify the register

Ask the user which register if not explicitly stated:
- **Working:** full structure, all evidence links, open questions visible
- **Team:** scoped to what the direct team owns; one delivery step per dependency owner
- **Executive:** one page, decision-first, no internal jargon, no `[TBD]` items visible (replace with "[under investigation]")

Default to Working if not specified.

### Step 3 — Draft the narrative

For Working register — write all sections:
- **Situation:** What is the problem and why does it matter now? (2–3 sentences)
- **Decision needed:** What does the reader need to decide or approve? (one sentence)
- **Options considered:** 2–3 options with tradeoffs (bullet format)
- **Recommendation:** One sentence recommendation, then 2–3 sentences of rationale citing PRD decisions
- **Sequencing:** Ordered phases or milestones. In Working register, link to work item IDs for traceability.
- **Open questions:** Unresolved items that affect the recommendation

For Executive register — strip to the essential:
- Remove work item ID references
- Remove all `[TBD]`/open-question sections
- Replace technical terms with plain language
- Target: one page readable in under 60 seconds

### Step 4 — Review with user

Show the draft. Ask whether tone, scope, and detail level match the intended audience. Adjust the register or content on feedback.

### Step 5 — Write the file

Write to `doc/Prioritization_Strategy/`. Filename: `{Product} - Strategy Brief - {MM.DD}.md`.

For Executive register: offer to produce a `pptx` deck via the `pptx` skill. Ask before invoking — this is the user's call.

---

## Output format

### Working register

```markdown
# Strategy Brief — {Product} — {YYYY-MM-DD}

_Sources: [`doc/PRD_Decisions/{prd-filename}`]({path}), [`doc/Delivery_WorkItems/_index.md`]({path})_

---

## Situation

{What is the problem and why does it matter now? 2–3 sentences.}

## Decision needed

{What the reader needs to decide or approve. One clear sentence.}

## Options considered

1. **{Option A}** — {1-sentence tradeoff summary}
2. **{Option B}** — {1-sentence tradeoff summary}
3. **{Option C / Status quo}** — {1-sentence tradeoff summary}

## Recommendation

**{Recommended option}.** {2–3 sentence rationale citing the PRD decisions that support it.}

## Sequencing

- **Phase 1 — {Name}:** {What gets built and why first. Work items: TBD-Epic-1}
- **Phase 2 — {Name}:** {What gets built next. Work items: TBD-Epic-2}

## Open questions

- [TBD] {Question that must be resolved before this can proceed}
```

### Executive register

```markdown
# {Product} — Strategy Brief

## Situation
{2 sentences. No internal jargon. No work item IDs. Decision-first framing.}

## What we need
{One clear ask — a decision, approval, or resource commitment.}

## Options
1. **{Option A}** — {tradeoff, one line}
2. **{Option B}** — {tradeoff, one line}

## Recommendation
{Option name} — {1–2 sentence rationale. No jargon.}

## Delivery plan
- **{Phase 1}:** {Plain-English milestone. When.}
- **{Phase 2}:** {Plain-English milestone. When.}
```
