# better-planning

Custom planning system with strict role separation. Three roles, three trust boundaries.

## Surfaces

- **`/planner <task>`** — produces a vetted implementation plan grounded in current code. Runs in the main orchestrator, not a subagent, so it can dispatch the others.
- **`/update-context [drift report]`** — refreshes `CLAUDE.md` from a focused audit (or an inbound drift report).

## Subagents

- **`explore-scoped`** — read-only, narrow file-bounded research. Returns terse, citation-heavy findings. Dispatched by `/planner` and `/update-context` when they need to verify a specific fact.
- **`update-context`** — the **sole writer** of `CLAUDE.md`. Translates a drift report into surgical edits. Never audits the codebase itself — the parent audits and reports; this agent translates.

## Rules

- **Single writer.** Only the `update-context` subagent has Edit access to `CLAUDE.md`. Every other surface is read-only on that file.
- **Verify, don't audit.** `update-context` confirms specific facts when needed; it does not go fishing for additional drift.
- **Citation discipline.** Every claim the planner makes about current behavior cites a `path:line`. If it can't be cited, it isn't known.
- **`CLAUDE.md` word budget: under 800.** Cuts are applied before edits push the file over.
- **No auto-edit on Conventions or Gotchas.** Those are human-authored institutional knowledge. The writer surfaces candidates in a "Flagged for human review" block — the parent routes to the user.

## When to invoke

Non-trivial tasks → `/planner <description>`. Periodic context maintenance → `/update-context`.
