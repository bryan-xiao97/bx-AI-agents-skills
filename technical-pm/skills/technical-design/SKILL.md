---
name: technical-design
description: >
  Turns a functional PRD — or a problem description the user provides — plus technical constraints into a technical design document. Reasons through multiple candidate approaches for each significant design problem, weighs their trade-offs, and recommends a path forward — then records the decisions and specs the chosen architecture (components, data flow, integrations, NFRs, risks). Use whenever the user says "technical design", "design the architecture", "think through the technical considerations", "what are the options", "weigh the trade-offs", "how should we build this", "what's the recommended approach", or is ready to turn requirements into a build-ready design. Every recommendation traces to a decision or requirement and the constraints that drove it. Outputs a self-contained HTML document to the doc/TechnicalDesign/ folder.
---

# technical-design

Produces a technical design document. The functional requirements say what and why; this works out how — by reasoning through options, not by asserting a single answer. For each significant design problem it lays out candidate approaches, weighs their trade-offs, and recommends one. Every recommendation traces to a decision or requirement and the constraints that drove it; nothing is invented.

## Workflow

### Step 1 — Gather the requirements

Work from whatever the user provides: a PRD, pasted requirements, or a direct description of the decisions and goals. If they point at a `doc/PRD_Decisions/` folder, read the most recent document. If the input is sparse, proceed with `[TBD]` placeholders and flag the gaps clearly. If nothing is supplied, ask for the requirements before designing.

Note the decisions, audience, constraints, and success signals in the source material — these anchor every technical choice that follows and become the yardstick for comparing approaches.

### Step 2 — Gather technical constraints

Ask the user for:
- Target system or platform (if known)
- Known technical constraints (security model, performance requirements, existing integrations, data sources)
- Priorities that break ties (e.g., time-to-market over cost, or auditability over speed)
- Stakeholders who will review the design

Document all responses. Anything unknown becomes `[TBD]` in the design. The stated priorities are what you weigh approaches against in Step 5 — get them explicit.

### Step 3 — Frame the design problems

Identify the **consequential** technical decisions this design has to make — the forks where choosing wrong is expensive to reverse. Aim for 1–4. These are problems like "how do we persist and query the data", "how do we authenticate multi-stakeholder access", "build vs. integrate for X" — not low-stakes implementation details.

For each problem, state in one sentence what is at stake and which PRD decision or constraint forces the choice. Skip problems that have only one sensible answer; name them in the design spec without ceremony.

### Step 4 — Confirm the problem framing with the user

Present the framed problems — each title and its one-sentence stake — before evaluating any approaches. Ask the user to confirm the list, add problems that are missing or drop ones that are not genuinely consequential. A wrong framing here wastes the entire trade-off analysis downstream, so do not proceed until the list is confirmed.

### Step 5 — Evaluate approaches and recommend

This is the core of the document. For **each** design problem from Step 3:

- **Lay out 2–3 genuine candidate approaches.** Each must be a real option someone could defend — not a strawman built to lose. Describe what each approach actually is.
- **Weigh trade-offs against the dimensions that matter for this problem.** Pick the 3–5 dimensions that actually discriminate the options — typically drawn from: complexity, build effort/time, cost, performance, scalability, security, maintainability, operational burden, and fit to the named constraints. Do not pad a table with dimensions where every approach scores the same.
- **Recommend one approach.** State it plainly, then give the rationale: why it wins *given the PRD decisions, constraints, and stated priorities from Step 2*. If the recommendation depends on an unconfirmed assumption, say so.

If a problem genuinely has only one viable approach, do not invent alternatives to fill the template — state the single approach and why no real alternative exists.

### Step 6 — Record the decisions

Consolidate the Step 5 recommendations into a compact decision record: one row per problem with the chosen approach, the one-line reason, the alternatives rejected, and the PRD decision it serves. This is the skimmable, traceable summary; the full reasoning lives in Step 5 above it.

### Step 7 — Draft the design spec

Spec the architecture that follows from the recommended approaches. Write the following, or `[TBD]` for anything not yet known:

- **System architecture:** Text-based description or block diagram of the system. Name the major components and show how they connect.
- **Components:** A table listing each component/service with a one-line purpose.
- **Data flow:** How data moves through the system, step by step. Name which component handles each step.
- **Integration points:** External systems, APIs, and data sources the system touches. For each: name, interaction direction (in/out/both), and a one-sentence description.
- **Dependencies:** What this system relies on (upstream), and what relies on this system (downstream).

### Step 8 — Draft non-functional requirements and risks

- **Non-functional requirements:** Performance, scale, security, availability, and compliance targets. Each NFR should be observable or measurable, not aspirational.
- **Risks and mitigations:** Technical risks that could derail delivery, each paired with a mitigation or an open question if no mitigation is known yet. Include risks introduced by the recommended approaches specifically.
- **Open technical questions:** Questions that must be answered before implementation can begin.

### Step 9 — Review with user

Present the full draft. Ask the user to:
- Challenge the recommendations — are the trade-offs weighed correctly, and do the stated priorities hold?
- Add any approach that was not considered
- Fill in any `[TBD]` items they already know
- Add missing constraints, integration points, NFRs, or risks

### Step 10 — Write the file

Write a **self-contained HTML document** to `doc/TechnicalDesign/`. Filename: `{Product} - Technical Design - {MM.DD}.html`. Report the full path on completion.

- Write a single HTML file with all CSS inlined in one `<style>` block — it must open in any browser and print to PDF with no external dependencies.
- Use semantic HTML (`<h1>`–`<h3>`, `<table>`, `<ol>`/`<ul>`, `<section>`). Keep the sections and their order below; that structure is the contract.
- Replace every `{placeholder}` and drop any `[TBD]` that got resolved. Grep the output for leftover `{` / `[TBD]` before declaring done.

**Styling — light, readable defaults, not a rigid system.** Aim for a calm document: charcoal text on white, a serif family for headings, sans for body, mono for small labels, and one warm accent. In the comparison-heavy parts, let color carry meaning — a green tint for the recommended option, a red/coral tint for rejected ones, amber for caution. Keep it restrained; don't over-engineer the CSS. **If the user wants the full house style, hand the content to the `bx-html-branding` skill** — its `.techdoc` kit is purpose-built for this document.

---

## Output format

Render these sections, in this order. Each was a heading in the former Markdown layout; keep them as `<h2>` sections (with the problem write-ups as `<h3>` subsections).

- **Title block** — document title (`{Product}` technical design) and a metadata line: date, a link to the source PRD (`doc/PRD_Decisions/{prd-filename}`), and the priorities that break trade-off ties (from Step 2).
- **Design problems and approaches** — one subsection per problem from Step 5. Each states *At stake* (one sentence), then 2–3 candidate approaches with their pros and cons, then a trade-off table comparing them across the discriminating dimensions, then a recommendation line naming the chosen approach and why. Visually distinguish the recommended option (e.g. a green-tinted column or card) and rejected ones.
- **Decision record** — a table: design problem, chosen approach, why (one line), alternatives rejected, and the PRD decision it serves.
- **System architecture** — prose describing the high-level structure, plus a simple text/block diagram (a `<pre>` block is fine) naming every major component.
- **Components** — a table of each component/service and its one-line purpose.
- **Data flow** — a numbered list of steps: which component acts, what happens, what data moves.
- **Integration points** — a table: system/API, direction (in/out/both), one-sentence description.
- **Dependencies** — upstream (what this system relies on) and downstream (what relies on it).
- **Non-functional requirements** — a table of observable/measurable targets (performance, scale, security, availability, compliance).
- **Risks and mitigations** — a table pairing each technical risk (including any introduced by the chosen approach) with a mitigation or open question; mark severity.
- **Open technical questions** — a list of `[TBD]` questions that must be answered before implementation.
