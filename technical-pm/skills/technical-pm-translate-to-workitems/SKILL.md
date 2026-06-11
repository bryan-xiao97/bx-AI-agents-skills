---
name: technical-pm-translate-to-workitems
description: >
  Decomposes a stage-3 PRD (and stage-4 technical design, if present) into an Epic → Feature → Story → Task hierarchy of work item drafts. Use whenever the user says "translate to work items", "break this down", "create the work items", "decompose into tasks", "what are the work items for this PRD", or is ready to turn a PRD into deliverable units. Produces local .md files with placeholder IDs — no external system calls, no ADO reads or writes. Writes to the 5_Delivery_WorkItems/ folder.
---

# technical-pm-translate-to-workitems

Decomposes PRD decisions into a local work item hierarchy. Placeholder IDs throughout — the user assigns real IDs when posting to their delivery system.

## Workflow

### Step 1 — Read the PRD and technical design

Locate and read the most recent `.md` file in `3_PRD_Decisions/`. If none exists, stop and route the user to `/technical-pm-write-prd` first.

Also read the most recent `.md` file in `4_TechnicalDesign/` if present — use it to inform Task-level decomposition, since components, integration points, and architecture decisions often map directly to tasks. If the folder is absent or empty, proceed from the PRD alone and note that tasks may need technical refinement once a design exists.

### Step 2 — Detect existing format

Check `5_Delivery_WorkItems/` for existing `.md` work item files. If files are present, open one and note:
- Header table fields and their order
- ID format
- Hierarchy notation in `## Hierarchy`
- Filename convention

If the folder is empty, use the default format in `## Output format` below.

### Step 3 — Build the hierarchy

Decompose PRD decisions into a work item tree:

| Type | Scope | Typical count |
|---|---|---|
| **Epic** | Large capability area | 1–3 per PRD |
| **Feature** | Deliverable chunk within an Epic | 2–5 per Epic |
| **User Story** | User-facing behavior within a Feature | 2–4 per Feature |
| **Task** | Concrete implementation step within a Story | Optional; include when clear from PRD |

Every work item must:
- Reference its parent by ID
- Trace back to the PRD decision or section that generated it (include the reference in the Description). When the PRD contains a Scope section, prefer citing the FR ID (e.g., `FR-3`) over a decision title or section name — fall back to decision title or section name only when no FR ID applies.

User stories follow the pattern: `As a {role}, I want {action} so that {outcome}.`

### Step 4 — Assign placeholder IDs

Use the form: `TBD-Epic-1`, `TBD-Feature-1.1`, `TBD-Story-1.1.1`, `TBD-Task-1.1.1.1`.

The user replaces these with real IDs when posting to their delivery system. Do not call any external system to check for collisions.

### Step 5 — Preview the hierarchy

Before writing any files, show the user:
1. The full hierarchy tree (indented bullet list with IDs and titles)
2. The list of filenames to be created

Wait for confirmation or corrections.

### Step 6 — Write all files

Write one `.md` per work item to `5_Delivery_WorkItems/`, plus an `_index.md`. Report the filenames and count on completion.

---

## Output format

### Work item file: `{ID}_{Type}_{Slug}.md`

```markdown
# [{ID}] {Type} — {Title}

| Field | Value |
|---|---|
| **State** | Draft |
| **Assigned To** | [TBD] |
| **Iteration** | [TBD] |
| **Area** | [TBD] |
| **Created** | {YYYY-MM-DD} |
| **Last Changed** | {YYYY-MM-DD} |
| **ADO Link** | [TBD] |

## Hierarchy

**Parent:** [{Parent ID} — {Parent Title}]({parent-filename}.md)

**Children:**
- [{Child ID} — {Child Title}]({child-filename}.md)

## Description

{For Epics/Features: capability description. For User Stories: "As a {role}, I want {action} so that {outcome}." For Tasks: specific implementation step.}

**PRD reference:** {FR ID from the PRD Scope section (e.g., FR-3) if present; otherwise decision title or section name from 3_PRD_Decisions/}
**Technical design reference:** {Component, integration, or architecture decision from 4_TechnicalDesign/, or [none] if no design exists yet}
```

### Index file: `_index.md`

```markdown
# Work Item Index — {Product}

_Generated from [`3_PRD_Decisions/{prd-filename}`]({relative path}) and [`4_TechnicalDesign/{design-filename}`]({relative path}) on {YYYY-MM-DD}._

## Hierarchy

- {TBD-Epic-1} — {Epic title}
  - {TBD-Feature-1.1} — {Feature title}
    - {TBD-Story-1.1.1} — {Story title}

## All items

| ID | Type | Title | State | Parent |
|---|---|---|---|---|
| TBD-Epic-1 | Epic | {Title} | Draft | — |
| TBD-Feature-1.1 | Feature | {Title} | Draft | TBD-Epic-1 |
| TBD-Story-1.1.1 | User Story | {Title} | Draft | TBD-Feature-1.1 |
```
