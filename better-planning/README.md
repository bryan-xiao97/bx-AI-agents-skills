# better-planning

Custom planning system with strict role separation.

## Agents

- **explore-scoped** (`agents/explore-scoped.md`) — Haiku. Read-only. Narrow file-bounded research, dispatched by the planner skill and by update-context.
- **update-context** (`agents/update-context.md`) — Sonnet. **The sole writer of CLAUDE.md.** Receives a drift report from its parent and applies surgical edits directly. Dispatched by the planner and update-context skills.

## Skills

- **planner** (`skills/planner/SKILL.md`) — runs the planner workflow in the main orchestrator (Opus). Activates when you describe a non-trivial implementation, refactor, or feature to plan. Produces a vetted plan, can dispatch explore-scoped for research, and can dispatch the update-context agent when drift is detected.
- **update-context** (`skills/update-context/SKILL.md`) — ad-hoc CLAUDE.md refresh. Activates when you ask to refresh context or hand over a drift report. Runs a focused mini-audit (or accepts a drift report) and dispatches the update-context agent. Shares a name with the agent it dispatches but is a distinct surface: the skill audits and dispatches, the agent writes.
- **subagent-execution** (`skills/subagent-execution/SKILL.md`) — executes an approved plan in-session. Dispatches a fresh implementer subagent per task, runs a spec-and-quality review after each and one broad review at the end, then hands back a completion summary. The planner recommends it once a plan with mostly-independent tasks is approved.

## Workflow

1. Start a session. CLAUDE.md loads automatically into the main agent's context.
2. For non-trivial tasks: describe what you want to plan → the planner skill activates, runs the planning workflow in the orchestrator, and returns a vetted plan with citations.
3. If the plan ends with "Context Drift Detected," decide: dispatch the update-context agent now (planner will offer), or proceed and refresh later.
4. To execute the approved plan in-session: the planner recommends the `subagent-execution` skill, which runs each task through a fresh implementer subagent and review loop, then summarizes for you.
5. To maintain CLAUDE.md on a cadence: ask to refresh CLAUDE.md → the update-context skill runs a focused audit, or scope it to a specific area.

## Design principles

- **Planner runs in the orchestrator.** The planning logic lives in a skill (Opus) loaded into the main context, not a subagent, so it can spawn explore-scoped and update-context via Task.
- **Single writer.** Only the `update-context` agent has Edit access to CLAUDE.md. Capability boundary matches trust boundary.
- **Writer isolation.** CLAUDE.md edits run in a Sonnet sub-agent so the planner's Opus context stays clean and the writer's tool surface stays narrow.
- **Template lives with the writer.** The CLAUDE.md template is baked into update-context's prompt. No drift between template and reality.
- **Citation discipline.** Every claim the planner makes about current behavior cites a `path:line`. If it can't be cited, the agent doesn't know it.
- **Git-based recency.** No daemon, no hooks. Git log is ground truth for recent activity.
- **User orchestrates.** Planner observes, update-context writes, you decide when to invoke each. No auto-updates.

## Files

```
├── README.md                       (this file)
├── agents/
│   ├── explore-scoped.md
│   └── update-context.md
└── skills/
    ├── planner/SKILL.md
    ├── update-context/SKILL.md
    └── subagent-execution/
        ├── SKILL.md
        ├── implementer-prompt.md
        └── task-reviewer-prompt.md

CLAUDE.md                           (maintained by the update-context agent)
```
