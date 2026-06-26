# better-planning

Custom planning system with strict role separation. Three roles, three trust boundaries.

## Surfaces

- **`planner` skill** — produces a vetted implementation plan grounded in current code. Activates when you describe a non-trivial implementation, refactor, or feature to plan. Runs in the main orchestrator, not a subagent, so it can dispatch the others.
- **`update-context` skill** — refreshes `CLAUDE.md` from a focused audit (or an inbound drift report). Activates when you ask to refresh context or hand over a drift report.
- **`subagent-execution` skill** — executes an approved plan in the current session. Dispatches a fresh implementer subagent per task, runs a spec-and-quality review after each and one broad review at the end, then hands back a completion summary. The planner recommends it once a plan with mostly-independent tasks is approved. Ships two dispatch templates (`implementer-prompt.md`, `task-reviewer-prompt.md`) beside its `SKILL.md`.

## Subagents

- **`explore-scoped`** — read-only, narrow file-bounded research. Returns terse, citation-heavy findings. Dispatched by the planner and update-context skills when they need to verify a specific fact.
- **`update-context`** — the **sole writer** of `CLAUDE.md`. Translates a drift report into surgical edits. Never audits the codebase itself — the parent audits and reports; this agent translates. (Same name as the `update-context` skill, but a distinct surface: the skill audits and dispatches, this agent writes.)

## Rules

- **Single writer.** Only the `update-context` agent has Edit access to `CLAUDE.md`. Every other surface — including the skills — is read-only on that file.
- **Verify, don't audit.** `update-context` confirms specific facts when needed; it does not go fishing for additional drift.
- **Citation discipline.** Every claim the planner makes about current behavior cites a `path:line`. If it can't be cited, it isn't known.
- **`CLAUDE.md` word budget: under 800.** Cuts are applied before edits push the file over.
- **No auto-edit on Conventions or Gotchas.** Those are human-authored institutional knowledge. The writer surfaces candidates in a "Flagged for human review" block — the parent routes to the user.

## When to invoke

Non-trivial tasks → describe the task and the `planner` skill activates. Periodic context maintenance → ask to refresh `CLAUDE.md` and the `update-context` skill activates.
