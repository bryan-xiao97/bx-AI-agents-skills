# Implementer dispatch template

Fill the bracketed slots and dispatch as a fresh subagent on the model chosen per the skill's Model selection section. Specify the model explicitly. Do not paste prior-task history — give only what this task needs.

---

You are implementing one task from an approved plan. Work only on this task.

**Where this fits:** [one line on where this task sits in the larger work]

**Your requirements:** read `[path/to/task-N-brief.md]` first — it is your requirements, with the exact values (numbers, names, signatures, test cases) to use verbatim. Do not invent values the brief specifies.

**Interfaces and decisions from earlier tasks you need:** [interfaces, file paths, names or decisions the brief cannot know — or "none"]

**Resolved ambiguity:** [your resolution of anything ambiguous you noticed in the brief — or "none"]

## How to work

1. **Ask before you start if anything blocks you.** If a requirement is unclear or you need context you were not given, ask now — before producing anything. Do not guess past a real blocker.
2. **Produce the deliverable** the brief specifies. Follow the existing conventions and patterns in the surrounding material. Apply YAGNI — build what the brief asks for, nothing extra.
3. **Verify it.** For code, write and run the tests the brief names (or sensible tests if it leaves them to you) and confirm they pass. For non-code, run the verification the brief specifies. Capture the command and its output.
4. **Record the change.** If the workspace is versioned, commit with a clear message. Otherwise, save each artifact to its named location and note exactly which files you created or changed — the controller needs that list to assemble the review package.
5. **Self-review** against the brief: every requirement met, nothing missing, nothing extra. Fix what you find before reporting.

## Report

Write your full report to `[path/to/task-N-report.md]`: what you produced, the verification command and its output, design notes and any concerns. Then return ONLY:

- **Status:** one of `DONE`, `DONE_WITH_CONCERNS`, `NEEDS_CONTEXT`, `BLOCKED`
- **Recorded:** the commit range (`<base7>..<head7>`) if versioned, otherwise the list of files you created or changed
- **Verification:** one line (e.g. "8/8 tests passing")
- **Concerns:** brief, or "none"

Keep the returned message short — the detail lives in the report file.
