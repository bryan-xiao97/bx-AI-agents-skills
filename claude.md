## Identity
Bryan Xiao — Technical Project Manager / Solutions Architect, Solomon Partners (Investment Bank), New York, NY
Manager: Paula Ruiz | Skip Manager: David Buza

## Stack & Context
- **Platform:** Copilot Studio (incl. VS Code extension), Power Platform (Power Automate, AI Builder), Azure DevOps
- **Domain:** Sell-side M&A — buyer list generation, CIM review, term sheet analysis, IOI/LOI review, due diligence, bid comparison
- **CLI:** Claude Code, GitHub Copilot CLI, OpenAI Codex CLI, m365 CLI
- **Integrations:** SharePoint, Outlook, Teams, Microsoft Graph API
- **DevOps:** Azure DevOps Git repos, pipeline YAML, PR gates, CI/CD, Power Platform ALM (managed/unmanaged solutions)

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

## In-flight work

### Gemini CLI dual-publishing (parked 2026-05-19)

**Status:** Plan drafted, no files written. Awaiting user confirmation on open assumptions before execution.

**Goal:** Make each of the 4 plugins (`azure`, `copilot-studio`, `product-manager`, `better-planning`) installable as both a Claude Code plugin AND a Gemini CLI extension, with zero file duplication for skills or agents.

**Key compatibility facts (verified 2026-05-17 via Gemini CLI docs):**
- `SKILL.md` is an open standard — same file works in both tools, no changes needed
- `agents/<name>.md` uses markdown + YAML frontmatter in both tools — schema overlaps but Claude's `tools: Bash(git log:*)` syntax may need a smoke test against Gemini's parser
- `commands/` is the only overlap point — Claude uses `.md` with `$ARGUMENTS`, Gemini uses `.toml` with `{{args}}`. Different extensions can coexist in the same folder.
- Claude's `.claude-plugin/plugin.json` and Gemini's root `gemini-extension.json` live at different paths and do not conflict
- Root `.claude-plugin/marketplace.json` is Claude-specific; Gemini ignores it
- `CLAUDE.md` and `GEMINI.md` are different filenames — coexist fine

**Files to create when resuming (6 total, no edits to existing files):**
1. `azure/gemini-extension.json`
2. `copilot-studio/gemini-extension.json`
3. `product-manager/gemini-extension.json`
4. `better-planning/gemini-extension.json`
5. `better-planning/commands/planner.toml` — TOML mirror of `planner.md` (convert `$ARGUMENTS` to `{{args}}`, drop `argument-hint` and `allowed-tools`)
6. `better-planning/commands/update-context.toml` — same conversion applied to `update-context.md`

Minimal manifest shape:
```jsonc
{
  "name": "<plugin-name>",
  "version": "0.1.0",
  "description": "<copy from .claude-plugin/plugin.json>"
}
```

**Open assumptions to confirm before execution:**
1. One Gemini extension per plugin (4 extensions) vs. one monolithic root extension. Plan assumes 4 separate extensions to mirror the Claude marketplace split.
2. Agent frontmatter `tools: Bash(git log:*)` may not parse in Gemini — needs smoke test on `better-planning/agents/explore-scoped.md`. Fallback is to relax to wildcards or move restriction to extension-level `excludeTools`.
3. `update-context` restructuring status (commit f4e5c57 mentions it but no `better-planning/skills/` directory exists yet) — confirm whether `update-context` is final in command+agent form.
4. Whether to set `contextFileName` per plugin (point at existing `README.md`, add new `GEMINI.md`, or leave off).
5. `README.md` lines 60-68 placeholder install URLs will need updating in parallel — out of scope for this task but flagged.

**Reference sources (verified 2026-05-17):**
- Gemini CLI Extensions: https://geminicli.com/docs/extensions/reference/
- Gemini CLI Skills: https://geminicli.com/docs/cli/skills/
- Gemini CLI Subagents: https://geminicli.com/docs/core/subagents/
- Gemini CLI Custom Commands: https://geminicli.com/docs/cli/custom-commands.html

**Resume command:** `/planner Resume the Gemini CLI dual-publishing work parked in CLAUDE.md.`
