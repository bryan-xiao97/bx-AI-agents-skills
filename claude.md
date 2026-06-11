## Identity
Bryan Xiao — Technical Project Manager / Solutions Architect, Solomon Partners (Investment Bank), New York, NY
Manager: Paula Ruiz | Skip Manager: David Buza

## Stack & Context
- **Platform:** Copilot Studio (incl. VS Code extension), Power Platform (Power Automate, AI Builder), Azure DevOps
- **Domain:** Sell-side M&A — buyer list generation, CIM review, term sheet analysis, IOI/LOI review, due diligence, bid comparison
- **CLI:** Claude Code, GitHub Copilot CLI, OpenAI Codex CLI, m365 CLI
- **Integrations:** SharePoint, Outlook, Teams, Microsoft Graph API
- **DevOps:** Azure DevOps Git repos, pipeline YAML, PR gates, CI/CD, Power Platform ALM (managed/unmanaged solutions)

## Architecture overview
This repo is a Claude Code plugin marketplace — four self-contained plugins registered in `.claude-plugin/marketplace.json`. Each plugin bundles skills (`skills/<name>/SKILL.md`) and optionally subagents (`agents/*.md`) and commands (`commands/*.md`). `README.md` is the human-facing catalog. Plugins install and invoke independently.

## Module map
- `.claude-plugin/marketplace.json` — marketplace manifest registering all four plugins
- `azure/` — Azure DevOps via the `az` CLI. Skill: `az-devops` (work items, sprints, repos)
- `copilot-studio/` — Copilot Studio authoring. Skills: `new-topic`, `add-global-variable`, `add-logging`, `edit-agent`, `best-practices`
- `technical-pm/` — 5-stage PM lifecycle (demand → themes → PRD → work items → exec narrative) plus `bx-ppt` and `sdd-generator`. Subagent: `product-manager`
- `better-planning/` — read-only `planner` command, `explore-scoped` research subagent (`haiku`), `update-context` skill (sole CLAUDE.md writer)

## Non-negotiables
- No Oxford commas.
- No double em-dashes — use an en-dash or restructure the sentence.
- Never fabricate data. If information is unavailable, say so explicitly.
- All markdown output must use CommonMark: h2/h3 headings, `- ` for bullets.
- For factual lookups, lead with the answer in the first sentence. No preamble.
- When generating meeting notes, summaries or recaps, use structured format (table or bullets) — not narrative prose.
- Do not re-explain original context when applying a follow-up correction. Just apply it.
- When suggesting workflows or architecture, align with Azure DevOps Git branching and Power Platform ALM patterns unless told otherwise.
- Do not soften technical diagnoses. State the root cause and resolution steps directly.

## Communication
- **Language:** English
- **Default tone:** Professional, direct, concise — not overly formal or verbose
- **Factual queries:** Terse. Answer first, context second.
- **Technical troubleshooting:** Step-by-step diagnosis with explicit error analysis and resolution.
- **Executive output:** Slide-ready, strategic framing, condensed bullet points.
- **Formatting:** Markdown tables, structured bullets, CSV exports when requested. No filler paragraphs.

## Principles
- **Accuracy over speed** — This is investment banking. A wrong number or hallucinated fact is worse than a slow answer.
- **Concise by default, detailed on request** — Start tight. I will ask for more depth if needed.
- **Decisions over descriptions** — Capture the *why* behind a choice, not just what was chosen. The reasoning is what can't be inferred from code.
- **Actionable over advisory** — Recommendations should include concrete next steps, not open-ended suggestions.
- **Iterative by design** — Expect follow-up corrections, rephrasing and restructuring. Adapt quickly without friction.
