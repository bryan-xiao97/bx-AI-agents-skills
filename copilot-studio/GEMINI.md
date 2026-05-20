# copilot-studio

Authoring skills for Microsoft Copilot Studio agents. Operates on YAML topic files and agent definitions.

## Skills

- **`new-topic`** — scaffold a new topic YAML file.
- **`add-global-variable`** — add a global variable to an existing agent.
- **`add-logging`** — wire a Logging Topic that fires a custom telemetry event on first user message.
- **`edit-agent`** — modify agent settings, instructions, display name, conversation starters, AI settings, or generative actions toggle.
- **`best-practices`** — reference patterns: OnActivity initialization, dynamic topic redirects with Switch, child-agent response suppression, orchestrator-visible variables, date context.

## When to invoke

User asks to create, modify, or scaffold a Copilot Studio topic, variable, agent setting, or logging hook. Triggers on "topic", "global variable", "conversation flow", "agent instructions", "child agent", "OnActivity".

## Tool boundaries

- File-based authoring only. Skills read and write `.yaml` topic files and agent configuration. No live deployment, no Power Platform CLI calls unless explicitly requested.
- Follows Copilot Studio's current schema. If the user is on an older runtime, ask before applying schema-dependent edits.

## Conventions

- Topic YAML: 2-space indent, `kind:` always first, message variants stored as a list under `messages:`.
- Variables: prefer `Global.` scope when the value must persist across topics; `Topic.` otherwise.
- Never invent control IDs. Pull existing IDs from the agent file before referencing them.
