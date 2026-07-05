---
name: surface-themes
description: >
  Synthesizes a set of demand notes — or any raw stakeholder feedback the user provides — into a theme cluster document. Use whenever the user says "surface themes", "what are the patterns", "synthesize the feedback", "find the common threads", "what do users actually want", or is ready to move from raw demand to a structured view of product needs. Each theme must cite at least 2 evidence sources from the provided input. Writes to the doc/Themes_Evidence/ folder.
---

# surface-themes

Clusters demand evidence into named themes. Every theme is grounded; nothing is invented.

## Workflow

### Step 1 — Gather the evidence

Work from whatever the user provides: pasted notes, one or more files, or a folder of demand notes (e.g. a `doc/User_Demand/` folder). If they point at a folder, read all `.md` files in it. If no input is supplied, ask for it before proceeding — themes need evidence to cluster.

### Step 2 — Extract evidence units

For each source, extract atomic evidence units: a stakeholder request, a pain point, a technical constraint, an observed behavior, a stated goal. For each unit, note:
- The **source filename**
- A **direct quote or close paraphrase** (keep it short — one sentence)
- The **underlying job-to-be-done** (what the stakeholder is trying to accomplish)

### Step 3 — Cluster into themes

Group evidence units into 3–7 themes. Keep clusters coherent and mutually exclusive. Name each theme as a short noun phrase (e.g., "Data Export Flexibility", "Secure Multi-Stakeholder Access").

Rules:
- A theme requires ≥2 distinct source citations. If a potential theme has only 1 source, fold it into a related theme or mark it `[Single source — verify]`.
- If two themes pull in opposite directions, do not merge them — name both and note the tension explicitly in a "Cross-cutting tensions" section.
- 3–7 themes is a guideline, not a hard cap. Use judgment based on the evidence volume.

### Step 4 — Draft the theme document

For each theme: name, one-sentence definition, ≥2 evidence citations with source filename and quote, and open questions.

### Step 5 — Review with user

Present the clusters before writing. Ask the user to:
- Confirm theme names and scope
- Merge, split, or rename any theme
- Surface any demand signal they feel is missing from the input

### Step 6 — Write the file

Write the final document to `doc/Themes_Evidence/`. Filename: `{Product} - Themes - {MM.DD}.md`. Report the full path on completion.

---

## Output format

```markdown
# Themes — {Product} — {YYYY-MM-DD}

_Synthesized from {N} source(s)._

---

## Theme 1: {Name}

**Definition:** {One sentence — what this theme means in concrete terms.}

**Evidence:**
- [{Source filename}]({relative path}) — "{direct quote or close paraphrase}"
- [{Source filename}]({relative path}) — "{direct quote or close paraphrase}"

**Open questions:** {What is still unclear about this theme, or [TBD] if none yet.}

---

## Theme 2: {Name}

...

---

## Cross-cutting tensions

{Optional. If two themes conflict or pull in opposite directions, name it here rather than burying it inside a theme definition.}
```
