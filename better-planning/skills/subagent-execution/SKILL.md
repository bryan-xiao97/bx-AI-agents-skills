---
name: subagent-execution
description: Use when you have an approved plan with mostly-independent tasks and want to execute it in the current session with quality gates. Dispatches a fresh implementer subagent per task, runs a per-task spec-and-quality review after each and one broad review across the whole run at the end — then hands you a completion summary. Pairs with the planner skill's output. Triggers on "execute this plan", "run the plan", "implement the plan", "build this out".
---

You are the controller. Your job is to execute an approved plan by dispatching a fresh subagent for each task, reviewing each task as it lands, running one broad review at the end, then summarizing for the user. You coordinate. You do not implement tasks yourself — that keeps your context clean for orchestration.

**Core principle:** fresh subagent per task + per-task review (spec compliance + quality) + broad final review = high quality, fast iteration.

**Why subagents:** you delegate each task to a specialized agent with isolated context. By crafting its instructions precisely, you keep it focused and give it exactly what it needs — it never inherits your session history. This also preserves your own context for coordination.

**Narration:** between tool calls, narrate at most one short line. The ledger and the tool results carry the record.

**Continuous execution:** do not pause to check in with the user between tasks. Execute every task in the plan without stopping. The only reasons to stop are a BLOCKED status you cannot resolve, ambiguity that genuinely prevents progress or all tasks complete. "Should I continue?" prompts waste the user's time — they asked you to execute the plan, so execute it.

This skill is deliverable-neutral. The worked examples are software (implement, run tests, commit) because that is the common case, but the same loop runs for a document, a process change or a system change. Read "produce the deliverable / verify it / record the change" wherever you see "implement / test / commit": the recorded result is a commit when the workspace is versioned, otherwise the saved artifact.

**Versioning is optional.** Do not assume the workspace is under version control. Where this skill mentions commits, branches or diffs, that is the convenience path when git (or a similar VCS) is present. When the workspace is unversioned, the implementer instead reports exactly which artifacts it created or changed, and you snapshot the files a task will touch before dispatching so the reviewer can see before-and-after. Decide which mode you are in once, at the start, and apply it throughout. Read "change-set" below as "the commit range if versioned, otherwise the set of created or changed artifacts".

## When to use

Walk these in order:

1. **Is there an approved plan?** No → this is not the skill. Plan first (use the `planner` skill), or execute manually.
2. **Are the tasks mostly independent?** No, they are tightly coupled → execute manually or re-plan to decouple them. Subagent isolation only pays off when tasks do not constantly depend on each other's in-flight state.
3. **Are you staying in this session?** Yes → use this skill. No, you want a separate or parallel execution session → use a separate session execution flow instead (parallel execution only if available).

## The process

For each task, in order:

1. **Write the task brief.** Extract the task's full text from the plan into a uniquely named scratch file (see [File handoffs](#file-handoffs)). The brief is the single source of requirements for that task.
2. **Capture the starting state, then dispatch the implementer** using `implementer-prompt.md`. Capture first so you can build the review package later: the current commit if versioned, otherwise a copy of the files this task is expected to touch (per the brief).
3. **Answer questions if asked.** If the implementer asks something before starting, answer clearly and completely, then let it proceed. Do not rush it past its questions.
4. **Implementer produces, verifies, records, self-reviews.** It returns a status (see [Handling implementer status](#handling-implementer-status)).
5. **Build the review package** for the task's change-set (see [File handoffs](#file-handoffs)).
6. **Dispatch the task reviewer** using `task-reviewer-prompt.md`. It returns two verdicts: spec compliance and quality.
7. **Fix loop.** Dispatch a fix subagent for every Critical and Important finding, then rebuild the package and re-review. Record Minor findings in the ledger for the final review to triage. Repeat until spec is ✅ and quality is approved.
8. **Mark the task complete** in your todo list and append one line to the ledger.

When no tasks remain:

9. **Final review across the whole run.** Build one package covering every change made across all tasks (the full branch diff if versioned, otherwise the union of every task's change-set) and dispatch the final reviewer on the most capable model. If it returns findings, dispatch ONE fix subagent with the complete list — never one fixer per finding.
10. **Completion summary.** Present the user a concise summary and stop. Do not integrate the work yourself (merge, PR, branch cleanup or equivalent) — the user decides next steps.

## Pre-flight plan review

Before dispatching Task 1, scan the plan once for conflicts:

- tasks that contradict each other or the plan's stated constraints
- anything the plan explicitly mandates that the review rubric would treat as a defect (a test that asserts nothing, verbatim duplication of a logic block)

Present everything you find to the user as one batched question — each finding beside the plan text that mandates it, asking which governs — before execution begins, not one interrupt per discovery mid-plan. If the scan is clean, proceed without comment. The review loop is still the net for conflicts that only surface during implementation.

## Model selection

Use the least powerful model that can handle each role, to conserve cost and increase speed. **Always specify the model explicitly when dispatching a subagent** — an omitted model inherits your session's model, often the most capable and most expensive, which silently defeats this section.

- **Transcription tasks** — the brief contains the complete content to produce, so the work is transcription plus verification: cheapest tier (e.g., Haiku 4.5).
- **Mechanical tasks** — isolated, 1-2 artifacts, complete spec, prose description to follow: cheap to standard tier.
- **Integration and judgment tasks** — multi-artifact coordination, pattern matching, debugging: standard tier (e.g., Sonnet 4.6).
- **Architecture and design tasks, plus the final whole-run review** — most capable tier (e.g., Opus 4.8).
- **Reviewers** — scale to the change-set's size, complexity and risk. A small mechanical change does not need the top tier; a subtle one does. Use a mid tier as the floor for reviewers.

Turn count beats token price: the cheapest models often take 2-3x the turns on multi-step work, costing more overall. When in doubt for a multi-step task, a mid tier is the safer floor.

Task complexity signals (implementation tasks):

- Touches 1-2 artifacts with a complete spec → cheap model
- Touches multiple artifacts with integration concerns → standard model
- Requires design judgment or broad understanding → most capable model

## Handling implementer status

Implementer subagents report one of four statuses:

- **DONE** — build the review package and dispatch the task reviewer.
- **DONE_WITH_CONCERNS** — the work is complete but the implementer flagged doubts. Read them first. If they bear on correctness or scope, address them before review. If they are observations (e.g. "this file is getting large"), note them in the ledger and proceed.
- **NEEDS_CONTEXT** — the implementer lacks information you did not provide. Provide it and re-dispatch.
- **BLOCKED** — assess the blocker. Context problem → add context, re-dispatch on the same model. Needs more reasoning → re-dispatch on a more capable model. Task too large → break it into smaller pieces. Plan itself is wrong → escalate to the user. Never ignore an escalation or force the same model to retry with no change — if it is stuck, something must change.

## Handling reviewer ⚠️ items

The task reviewer may report "⚠️ cannot verify from change-set" items — requirements that live in unchanged material or span tasks. These do not block the rest of the review, but you must resolve each one yourself before marking the task complete, because you hold the plan and cross-task context the reviewer lacks. If you confirm an item is a real gap, treat it as a failed spec review: send it back to the implementer and re-review.

## Constructing reviewer prompts

Per-task reviews are task-scoped gates. The broad review happens once, at the end. When you fill the reviewer template:

- **Do not pre-judge findings.** Never instruct a reviewer to ignore a specific issue, to not flag something or to rate a finding "Minor at most". If you believe a finding is a false positive, let the reviewer raise it and adjudicate it in the review loop. If the prompt you are writing contains "do not flag", "don't treat X as a defect" or "the plan chose" — stop, you are pre-judging to spare yourself a loop.
- **Do not add open-ended directives** like "check all uses" without a concrete, task-specific reason.
- **Do not ask the reviewer to re-run tests the implementer already ran** on the same code — the implementer's report carries the evidence.
- **Copy the binding constraints verbatim** from the plan into the reviewer's global-constraints block: exact values, exact formats and the stated relationships between components ("same layout as X", "matches Y"). This block is the reviewer's attention lens — the template already carries the process rules.
- **Hand the change-set as a file**, not pasted text (see [File handoffs](#file-handoffs)).
- **One task per dispatch.** Do not paste accumulated prior-task summaries into later dispatches. A fresh subagent needs its task, the interfaces it touches and the constraints. Nothing else.
- **A plan-mandated finding is the user's call.** If a finding conflicts with what the plan's text requires, present the finding and the plan text and ask which governs. Do not dismiss it because the plan mandates it, and do not dispatch a fix that contradicts the plan without asking.
- **Every fix dispatch carries the implementer contract:** the fix subagent re-runs the verification covering its change and reports the result. Name the covering tests or check in the dispatch. Before re-dispatching the reviewer, confirm the fix report contains the check, the command run and the output.

## File handoffs

Everything you paste into a dispatch prompt, and everything a subagent prints back, stays resident in your context for the rest of the session and is re-read on every later turn. Hand artifacts over as files. Use the session scratchpad directory for all of these.

- **Task brief:** before dispatching an implementer, extract the task's full text from the plan into a uniquely named file (e.g. `task-N-brief.md`). Your dispatch then contains: (1) one line on where this task fits; (2) the brief path, introduced as "read this first — it is your requirements, with the exact values to use verbatim"; (3) interfaces and decisions from earlier tasks that the brief cannot know; (4) your resolution of any ambiguity you noticed in the brief; (5) the report-file path and the report contract. Exact values (numbers, signatures, test cases) live only in the brief.
- **Report file:** name it after the brief (`task-N-brief.md` → `task-N-report.md`) and pass it in the dispatch. The implementer writes its full report there and returns only status, what it recorded, a one-line verification summary and concerns.
- **Review package:** build it yourself for the task's change-set and pass the reviewer the path to one uniquely named file.
  - **Versioned workspace** (git): redirect the commit list, stat and diff into the file, using the BASE commit you captured at step 2 — never `HEAD~1`, which silently truncates multi-commit tasks:

    ```bash
    { git log --oneline "$BASE"..HEAD; echo; git diff --stat "$BASE"..HEAD; echo; git diff -U10 "$BASE"..HEAD; } > task-N-package.txt
    ```

  - **Unversioned workspace:** list every artifact the implementer created or changed and, for each, include enough to review it — the new content, plus a `diff` against the before-state you snapshotted at step 2 (or the whole file when it is new with no prior version).
- **Reviewer inputs:** the task reviewer gets three paths — the brief, the report and the package — plus the verbatim constraints block.
- **Fix handoffs:** fix subagents append their fix report (with the verification result) to the same report file and return a short summary. Re-reviews read the updated file.

## Durable progress

Conversation memory does not survive compaction. A controller that loses its place can re-dispatch an entire completed task sequence — the single most expensive failure. Track progress in a ledger file, not only in todos.

- The ledger lives at `<workspace-root>/.better-planning/sdd-progress.md` (scratch; git-ignored when the workspace uses git). At skill start, read it. Tasks marked complete there are DONE — do not re-dispatch them; resume at the first task not marked complete.
- When a task's review comes back clean, append one line in the same message as your other bookkeeping: `Task N: complete (<change-set ref>, review clean)`, where the ref is the commit range if versioned, otherwise the artifact paths.
- The ledger is your recovery map: the change-set it names persists on disk (and in `git log`, if versioned) even when your context no longer remembers producing it. After compaction, trust the ledger over your own recollection; cross-check against `git log` when the workspace is versioned.
- If the ledger is lost (e.g. `git clean -fdx` in a versioned workspace), reconstruct it from whatever durable record remains — `git log` if versioned, otherwise the per-task report files in scratch and the saved artifacts.

## Prompt templates

- `implementer-prompt.md` — dispatch the implementer subagent.
- `task-reviewer-prompt.md` — dispatch the task reviewer subagent (spec compliance + quality).
- Final review across the whole run: reuse `task-reviewer-prompt.md` scoped to every change-set, or `superpowers:requesting-code-review`'s `code-reviewer.md` if installed. For code, the general `code-reviewer` agent works; for non-code, a `general-purpose` reviewer subagent.

## Completion summary

After the final review is clean, present the user a concise summary and stop:

- tasks completed, with their change-sets (commit ranges or saved artifacts)
- the final review outcome
- any open Minor findings the user may want to address
- a clear handoff: the work is done and verified; the user decides on integration and further work

Do not integrate the work yourself (merge, PR, branch cleanup or equivalent) — that is the user's decision.

## Red flags

Never:

- In a versioned workspace, start execution directly on a shared mainline (e.g. git `main`/`master`) without explicit user consent
- Skip task review, or accept a report missing either verdict (spec compliance AND quality are both required)
- Move to the next task while the current review has open Critical or Important findings
- Dispatch multiple implementer subagents in parallel (they conflict)
- Make a subagent read the whole plan file — hand it its task brief instead
- Skip scene-setting context — the subagent needs to understand where its task fits
- Ignore a subagent's questions — answer before letting it proceed
- Accept "close enough" on spec compliance — the reviewer finding spec issues means not done
- Tell a reviewer what not to flag, or pre-rate a finding's severity in the dispatch prompt
- Dispatch a task reviewer without a package file — build it first
- Let the implementer's self-review replace the actual review — both are needed
- Re-dispatch a task the ledger already marks complete — check the ledger (and `git log` when versioned) after any compaction or resume
- Dispatch one fixer per finding on the final review — send ONE fix subagent the complete list
