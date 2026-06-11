---
name: technical-pm-technical-design
description: >
  Turns a stage-3 functional PRD plus user-supplied technical constraints into a technical design document. Reasons through multiple candidate approaches for each significant design problem, weighs their trade-offs, and recommends a path forward — then records the decisions and specs the chosen architecture (components, data flow, integrations, NFRs, risks). Use whenever the user says "technical design", "design the architecture", "think through the technical considerations", "what are the options", "weigh the trade-offs", "how should we build this", "what's the recommended approach", or is ready to move from a functional PRD to a build-ready design. Every recommendation traces to a PRD decision and the constraints that drove it. Writes to the 4_TechnicalDesign/ folder.
---

# technical-pm-technical-design

Produces the stage-4 technical design document. The functional PRD says what and why; this works out how — by reasoning through options, not by asserting a single answer. For each significant design problem it lays out candidate approaches, weighs their trade-offs, and recommends one. Every recommendation traces to a PRD decision and the constraints that drove it; nothing is invented.

## Workflow

### Step 1 — Confirm upstream exists

Check for a `3_PRD_Decisions/` document for this product. If none exists, stop and route the user to `/technical-pm-write-prd` first. If a PRD exists but is sparse, proceed with `[TBD]` placeholders and flag the gaps clearly.

Read the most recent PRD. Note its decisions, audience, constraints, and success signals — these anchor every technical choice that follows and become the yardstick for comparing approaches.

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

Write to `4_TechnicalDesign/`. Filename: `{Product} - Technical Design - {MM.DD}.md`. Report the full path on completion.

---

## Output format

````markdown
# Technical Design — {Product} — {YYYY-MM-DD}

_PRD sourced from: [`3_PRD_Decisions/{prd-filename}`]({relative path})_

**Priorities driving trade-offs:** {What breaks ties — e.g., time-to-market > cost > operational simplicity. From Step 2.}

---

## Design problems and approaches

### Problem 1: {Short title}

**At stake:** {One sentence — what this decision determines, and which PRD decision or constraint forces it.}

**Candidate approaches:**

- **Approach A — {name}:** {What it is.}
  - *Pros:* {…}
  - *Cons:* {…}
- **Approach B — {name}:** {What it is.}
  - *Pros:* {…}
  - *Cons:* {…}
- **Approach C — {name}:** {What it is.} *(omit if only two real options)*
  - *Pros:* {…}
  - *Cons:* {…}

**Trade-offs:**

| Dimension | Approach A | Approach B | Approach C |
|---|---|---|---|
| {Dimension that discriminates} | {…} | {…} | {…} |
| {Dimension that discriminates} | {…} | {…} | {…} |

**Recommendation: Approach {X}.** {Why it wins given the PRD decisions, constraints, and stated priorities. Note any assumption the recommendation rests on.}

### Problem 2: {Short title}

...

---

## Decision record

| Design problem | Chosen approach | Why | Alternatives rejected | PRD decision |
|---|---|---|---|---|
| {Problem 1 title} | {Approach X} | {One line} | {The other approaches} | {Decision title from stage-3} |
| {Problem 2 title} | {Approach Y} | {One line} | {…} | {…} |

---

## System architecture

{Describe the high-level system structure that follows from the recommended approaches. Use a text block diagram or prose. Name every major component.}

```
[Component A] → [Component B] → [Component C]
                      ↑
               [External System]
```

## Components

| Component | Purpose |
|---|---|
| {Name} | {One-line description} |
| {Name} | {One-line description} |

## Data flow

1. {Step — which component, what happens, what data moves}
2. {Step}
3. {Step}

## Integration points

| System / API | Direction | Description |
|---|---|---|
| {Name} | In / Out / Both | {One sentence} |

## Dependencies

**Upstream (this system relies on):** {list}
**Downstream (relies on this system):** {list}

---

## Non-functional requirements

| Requirement | Target |
|---|---|
| {Performance / scale / security / availability / compliance} | {Observable or measurable target} |

## Risks and mitigations

| Risk | Mitigation |
|---|---|
| {Technical risk, including any introduced by the chosen approach} | {Mitigation, or [TBD] open question} |

## Open technical questions

- [TBD] {Question that must be answered before implementation}
````
