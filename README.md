# bx-AI-agents-skills

Personal plugin collection for Azure DevOps, Microsoft Copilot Studio authoring, product management workflows, and project-planning discipline. Dual-published as both [Claude Code plugins](https://docs.claude.com/en/claude-code/plugins) and Antigravity CLI / Gemini CLI extensions.

## Overview

This repo is a plugin marketplace containing four plugins. Each plugin groups related skills (and optionally subagents) into a self-contained directory that either Claude Code or the Antigravity CLI (formerly Gemini CLI) can install and invoke directly. `SKILL.md` follows the open Agent Skills standard, so each skill works identically in both tools. Subagent and command files differ slightly between ecosystems — both formats are shipped side-by-side.

## Plugins

### `azure` — Azure DevOps CLI

Interact with Azure DevOps via the `az` CLI without leaving Claude Code.

| Skill | What it does |
|-------|-------------|
| `az-devops` | Query and browse work items (epics, features, stories, tasks, bugs), view sprint boards, drill into parent-child hierarchies, and list Git repositories. |

### `copilot-studio` — Copilot Studio Authoring

Author and maintain Microsoft Copilot Studio agents from the command line.

| Skill | What it does |
|-------|-------------|
| `new-topic` | Scaffold a new topic YAML file for a Copilot Studio agent. |
| `add-global-variable` | Add a global variable to an existing agent. |
| `add-logging` | Wire up logging nodes to a topic. |
| `edit-agent` | Make structural edits to an existing agent definition. |
| `best-practices` | Reference patterns for orchestrator variables, date context, child-agent response suppression, and topic redirects with variable passing. |

### `product-manager` — PM Lifecycle

End-to-end product management across a five-stage lifecycle, from raw stakeholder input to exec-ready strategy. Includes a `product-manager` subagent that orchestrates the full flow.

**Five stages:**

| Stage | Skill | Output |
|-------|-------|--------|
| 1 — Capture demand | `pm-capture-demand` | Structured demand note from meeting transcripts, demo notes, or stakeholder emails |
| 2 — Surface themes | `pm-surface-themes` | Clustered theme document, each theme backed by ≥2 demand sources |
| 3 — Author PRD | `pm-write-prd` | Product + technical design spec with decisions, alternatives rejected, and constraints named |
| 4 — Translate to work items | `pm-translate-to-workitems` | Epic → Feature → Story → Task drafts as local Markdown files |
| 5 — Exec narrative | `pm-exec-narrative` | One-page executive summary derived from the work item plan and PRD |

**Additional skills:**

| Skill | What it does |
|-------|-------------|
| `bx-ppt` | Generate a PowerPoint deck from a structured input using `pptxgenjs` (cross-platform). |
| `bx-ppt-COM` | Windows-specific variant that drives PowerPoint via COM automation for richer fidelity. |
| `sdd-generator` | Generate a Software Design Document from a PRD or set of work items. |

### `better-planning` — Planning System

Custom planning system with strict role separation. A read-only planner produces vetted implementation plans; a scoped research agent verifies specific facts on demand; a sole-writer agent maintains `CLAUDE.md` / `GEMINI.md`.

| Command / Agent | What it does |
|-----------------|-------------|
| `/planner <task>` | Produces a vetted implementation plan grounded in current code, with citations. |
| `/update-context` | Audits the project context file for drift and dispatches the writer agent. |
| `explore-scoped` (agent) | Read-only, narrow file-bounded research. Dispatched by `/planner` and `/update-context`. |
| `update-context` (agent) | Sole writer of `CLAUDE.md` / `GEMINI.md`. Translates a drift report into surgical edits. |

## Installation

### Claude Code

> Requires Claude Code with plugin support enabled.

**Install all plugins at once:**

```bash
claude plugin install https://github.com/bryanxiao/bx-AI-agents-skills
```

**Or install a single plugin:**

```bash
claude plugin install https://github.com/bryanxiao/bx-AI-agents-skills/azure
claude plugin install https://github.com/bryanxiao/bx-AI-agents-skills/copilot-studio
claude plugin install https://github.com/bryanxiao/bx-AI-agents-skills/product-manager
claude plugin install https://github.com/bryanxiao/bx-AI-agents-skills/better-planning
```

### Antigravity CLI / Gemini CLI

> Requires the Antigravity CLI (formerly Gemini CLI). Each plugin ships a `gemini-extension.json` manifest and a per-plugin `GEMINI.md` context file.

**Install each plugin as a separate extension:**

```bash
gemini extensions install https://github.com/bryanxiao/bx-AI-agents-skills/azure
gemini extensions install https://github.com/bryanxiao/bx-AI-agents-skills/copilot-studio
gemini extensions install https://github.com/bryanxiao/bx-AI-agents-skills/product-manager
gemini extensions install https://github.com/bryanxiao/bx-AI-agents-skills/better-planning
```

Skills are auto-discovered under `~/.gemini/extensions/<plugin>/skills/`. Subagents register from each plugin's `agents/*.md`. Commands are exposed via `commands/*.toml` (Gemini) and `commands/*.md` (Claude) — both formats ship side-by-side.

### After installation

Skills are available as slash commands (e.g. `/az-devops`, `/pm-write-prd`, `/planner`). Subagents (`product-manager`, `explore-scoped`, `update-context`) are available via the agent picker.

## Repository Layout

```
bx-AI-agents-skills/
├── .claude-plugin/
│   └── marketplace.json        # Claude Code marketplace manifest
├── azure/
│   ├── .claude-plugin/plugin.json   # Claude plugin manifest
│   ├── gemini-extension.json        # Antigravity / Gemini extension manifest
│   ├── GEMINI.md                    # Plugin-scoped Gemini context
│   └── skills/
│       └── az-devops/
│           └── SKILL.md
├── copilot-studio/
│   ├── .claude-plugin/plugin.json
│   ├── gemini-extension.json
│   ├── GEMINI.md
│   └── skills/
│       ├── add-global-variable/
│       ├── add-logging/
│       ├── best-practices/
│       ├── edit-agent/
│       └── new-topic/
├── product-manager/
│   ├── .claude-plugin/plugin.json
│   ├── gemini-extension.json
│   ├── GEMINI.md
│   ├── agents/
│   │   └── product-manager.md
│   └── skills/
│       ├── bx-ppt/
│       ├── bx-ppt-COM/
│       ├── pm-capture-demand/
│       ├── pm-exec-narrative/
│       ├── pm-surface-themes/
│       ├── pm-translate-to-workitems/
│       ├── pm-write-prd/
│       └── sdd-generator/
└── better-planning/
    ├── .claude-plugin/plugin.json
    ├── gemini-extension.json
    ├── GEMINI.md
    ├── agents/
    │   ├── explore-scoped.md
    │   └── update-context.md
    └── commands/
        ├── planner.md      # Claude command (uses $ARGUMENTS)
        ├── planner.toml    # Antigravity / Gemini command (uses {{args}})
        ├── update-context.md
        └── update-context.toml
```

Each skill lives in its own directory and exposes a `SKILL.md` as its entry point (open Agent Skills standard — same file works in both Claude and Antigravity/Gemini). Subagents are defined in `agents/*.md`. Slash commands ship in both Claude `.md` and Antigravity/Gemini `.toml` forms.

## Adding a New Skill

1. Create a directory under the relevant plugin's `skills/` folder.
2. Add a `SKILL.md` with a YAML front-matter block. Required fields: `name`, `description`. Optional Claude-only fields: `argument-hint`, `allowed-tools`. Antigravity/Gemini ignores anything beyond `name` and `description`, so the file is portable as long as those two are present.
3. Add the skill name to the plugin's `plugin.json` if required by your Claude Code version.
4. Test by reloading the plugin in Claude Code (and/or running `gemini extensions install` for Antigravity) and invoking the skill.

To add a new plugin, create a top-level directory with its own `.claude-plugin/plugin.json` and `gemini-extension.json`, then register it in `.claude-plugin/marketplace.json`. If the plugin should ship a slash command, provide both `.md` (Claude) and `.toml` (Antigravity/Gemini) variants under `commands/`. If it has subagents, keep the `tools:` array to plain tool names and wildcards — Claude-style `Bash(<glob>)` patterns do not parse in Antigravity/Gemini.
