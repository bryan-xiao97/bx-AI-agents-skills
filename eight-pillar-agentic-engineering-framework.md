# Eight Pillars of Agentic Software Engineering

**A practical framework for reliable, observable and governed coding-agent systems**  
**Initial tool assessment:** Claude Code  
**Prepared:** July 15, 2026

---

## Executive Summary

Frontier language models can generate strong code, but model capability alone does not reliably produce correct, maintainable software over long-running tasks. Coding agents must interpret ambiguous intent, inspect unfamiliar repositories, manage finite context, use development tools, recover from failures, validate behavior and operate within acceptable security and cost boundaries.

The core engineering challenge is therefore not simply selecting the model that writes the best code. It is designing the **model-harness-environment configuration** that can consistently convert human intent into verified software changes.

This report synthesizes two complementary bodies of research:

1. **Harness-oriented research**, focused on the recurring capabilities implemented by frontier coding environments such as Claude Code, Codex, Grok Build and Antigravity
2. **Practitioner-oriented research**, focused on the writings, testimonials and operating practices of engineering leaders and teams building or using agentic systems

Together, these perspectives support an eight-pillar framework for agentic software engineering:

1. Stateful reconciliation loop
2. Specification and unit-of-work design
3. Context and knowledge engineering
4. Tool-enabled execution environment
5. Harness-enforced verification
6. Single accountable coordination authority
7. Isolated execution with conditional concurrency
8. Observable, economical and bounded operation

The central thesis is:

> **Reliable agentic software engineering comes from placing a capable model inside a stateful, legible and bounded delivery system with short executable feedback loops, enforceable validation and one accountable coordinating authority.**

Claude Code implements most of the technical primitives required by this framework, including iterative tool use, repository instructions, memory, planning, subagents, hooks and permissions. Its most important limitations are not missing product features. They are the areas that no coding harness can completely solve by itself: specification quality, decomposition quality, independent acceptance, human review capacity, tamper-resistant project controls and economic governance.

---

## 1. Problem Statement

Traditional coding assistance primarily helps a developer produce or explain code in small increments. Agentic coding changes the operating model. The agent can inspect files, execute commands, edit a repository, launch tests and continue working with limited intervention.

This creates a more demanding reliability problem. An autonomous or semi-autonomous coding agent must be able to:

- Understand the desired outcome and relevant constraints
- Discover the actual state of the repository and environment
- Break substantial work into tractable units
- Select and execute appropriate tools
- Interpret failures and update its approach
- Preserve state across long sessions or context boundaries
- Demonstrate that the resulting behavior satisfies acceptance criteria
- Avoid unauthorized, destructive or unnecessarily expensive actions
- Produce artifacts that a human can review and ultimately own

A powerful model without these supporting structures may generate plausible code while losing track of the original requirement, relying on stale assumptions, stopping after partial completion or asserting success without sufficient evidence.

The surrounding **agent harness** is therefore part of the engineering system. It controls how the model receives instructions, selects context, invokes tools, maintains state, requests approval, delegates work, validates results and communicates progress.

The practical question for engineering organizations is no longer only:

> Which model writes the best code?

It is:

> **Which model, harness, repository and execution environment most reliably turns intent into verified software changes within our risk, cost and review constraints?**

---

## 2. Framework Thesis and Design Principles

The eight pillars are based on several cross-cutting principles.

### 2.1 The model is one component of a larger system

Agent performance depends on the quality of the model, but also on tool schemas, repository legibility, context selection, execution isolation, environmental feedback and stopping conditions. Two products using similarly capable models can behave differently because their harnesses expose different information and enforce different operational boundaries.

### 2.2 Ground truth must come from the environment

The agent should not rely on its own narrative of what it changed. It should inspect files, execute commands, read exit codes, review diffs and compare actual behavior with acceptance criteria. Claims of completion should remain grounded in observable state.

### 2.3 Persistent artifacts are more reliable than conversational memory

Plans, task ledgers, commits, test results and handoff notes provide durable continuity. They reduce dependence on the model reconstructing important decisions from a long or compressed transcript.

### 2.4 Autonomy should scale with verification capacity

More autonomy is useful only when the system can detect mistakes and a responsible human can assess the resulting evidence. Agent throughput that exceeds automated and human review capacity can increase organizational risk rather than productivity.

### 2.5 Isolation and concurrency are separate decisions

Isolation is broadly useful for safety, clean state and conflict reduction. Concurrency is conditional. Parallel coding is most effective when tasks are genuinely independent, ownership boundaries are explicit and integration can be reviewed coherently.

### 2.6 One authority must remain accountable for the result

Agents can plan, implement, test and review, but consequential software changes still require a clearly identified authority responsible for synthesis, acceptance and release. In current engineering practice, that authority should normally be human.

---

## 3. The Eight-Pillar Framework

### Pillar 1: Stateful Reconciliation Loop

#### Definition

Continuously reconcile the observed repository and runtime state against a persisted goal, plan and task ledger.

#### Why it matters

The foundational agent pattern is an iterative loop:

1. Understand the objective
2. Inspect the current state
3. Choose an action
4. Execute a tool
5. Observe the result
6. Validate progress
7. Revise the plan or continue

The loop becomes reliable only when it is both **stateful** and **reconciliatory**. Stateful means that goals, decisions, completed work and unresolved issues survive beyond a single inference turn. Reconciliation means the agent repeatedly compares its desired state with real evidence from the environment.

This is more precise than asking an agent to “keep trying.” The agent needs an explicit representation of what should be true and a reliable method for determining what is currently true.

#### Required capabilities

- Tool results return to the model as evidence for the next decision
- A plan or task ledger records desired and completed state
- The agent can inspect files, diffs, test output and runtime behavior
- Progress survives context compaction, session boundaries or handoffs
- Failed attempts remain available when they contain useful diagnostic information
- The loop has explicit completion and termination conditions

#### Failure modes

- Acting on assumptions without inspecting actual state
- Repeating failed approaches because the failure was not preserved
- Losing the original objective as the context window fills
- Mistaking partial progress for completion
- Continuing indefinitely without a stopping condition

#### Recommended organizational practice

Require substantial agent tasks to maintain a durable plan or progress artifact. Treat the repository, issue tracker or task ledger as the authoritative state rather than the conversation transcript alone.

---

### Pillar 2: Specification and Unit-of-Work Design

#### Definition

Translate intent into bounded, dependency-aware work units with explicit acceptance criteria.

#### Why it matters

Agents perform poorly when broad, ambiguous requests are treated as one indivisible task. Effective agentic delivery starts by defining the problem, identifying constraints and dividing the work into coherent increments.

Unit-of-work management is not merely tracking a checklist. The difficult part is selecting slices that are:

- Small enough to reason about and verify
- Large enough to deliver a coherent outcome
- Explicit about dependencies and ownership
- Compatible with the architecture of the system
- Accompanied by concrete acceptance criteria

Decomposition quality becomes even more important when multiple agents are involved. A vague requirement or poor module boundary can multiply inconsistent decisions across parallel runs.

#### Required capabilities

- A planning phase for ambiguous or cross-cutting work
- Clear task boundaries and expected outputs
- Dependency and sequencing awareness
- Acceptance criteria for every meaningful increment
- A definition of done that includes validation
- A mechanism for updating the plan when execution reveals new facts

#### Failure modes

- Attempting a large feature in one pass
- Creating tasks that are individually complete but collectively inconsistent
- Splitting work along convenient file boundaries rather than architectural boundaries
- Omitting tests, migration work, documentation or rollout considerations
- Treating task completion as evidence that the business objective was satisfied

#### Recommended organizational practice

Separate planning from execution for vague or substantial changes. Have the human owner review the plan, task boundaries and acceptance criteria before high-cost implementation begins.

---

### Pillar 3: Context and Knowledge Engineering

#### Definition

Curate the model's active context while externalizing durable repository knowledge and procedural memory.

#### Why it matters

A context window is finite. Its practical quality can degrade before its formal token limit is reached. Long command output, repeated file reads, stale assumptions and unrelated exploration can crowd out the instructions and evidence most relevant to the current decision.

Context engineering is therefore not simply adding more information. It is selecting the right information at the right time.

A mature system distinguishes among:

- **Always-loaded guidance:** concise repository conventions and critical constraints
- **Scoped guidance:** instructions relevant only to particular files or components
- **Procedural knowledge:** reusable workflows or skills loaded when needed
- **Durable memory:** plans, decisions, test results and progress stored outside the active context
- **Ephemeral evidence:** command output and observations needed for the current loop

#### Required capabilities

- Repository-local instruction files
- Path-scoped rules or conditional guidance
- Reusable skills or procedures
- External memory through files, commits and task artifacts
- Context inspection and compaction
- Separate contexts for delegated research or implementation
- A process for removing stale, redundant or conflicting instructions

#### Failure modes

- Overloading one instruction file with every possible rule
- Assuming persistent memory is equivalent to enforced policy
- Allowing stale guidance to override current repository reality
- Keeping large exploration logs in the main implementation context
- Compacting away unresolved decisions or acceptance criteria

#### Recommended organizational practice

Keep always-loaded instructions concise and testable. Place detailed workflows in skills or scoped rules. Review repository guidance as maintained engineering documentation rather than as a one-time prompt.

---

### Pillar 4: Tool-Enabled Execution Environment

#### Definition

Connect model reasoning to reality through controlled access to files, shells, browsers, tests and development services.

#### Why it matters

An agent cannot reliably engineer software from pasted snippets alone. It needs access to the actual environment in which the software is built and evaluated.

The useful tool surface may include:

- File discovery and search
- Structured file editing
- Shell or PowerShell execution
- Git operations and diff inspection
- Package managers and dependency tooling
- Compilers, linters and type checkers
- Unit and integration test runners
- Local applications and browser automation
- External services exposed through MCP or plugins

Tool breadth is not sufficient by itself. Tools must return legible output, preserve exit codes and expose changed state. A silent or ambiguous tool failure can disconnect the agent's reasoning from reality.

#### Required capabilities

- Access to repository-native engineering tools
- Clear success and failure signals
- Controlled credentials and network access
- Visible file and workspace mutations
- Reliable command execution and timeout handling
- Extensibility for project-specific workflows
- Tool access scoped to the task and agent role

#### Failure modes

- Missing dependencies or inconsistent local environments
- Tool output too large or unclear for the agent to interpret
- Credentials exposed to an unnecessarily broad execution surface
- MCP servers or extensions treated as trusted without review
- Differences between the agent environment and CI or production

#### Recommended organizational practice

Create reproducible development environments and one-command quality workflows. Treat agent-facing tools as production interfaces that require clear contracts, predictable outputs and security review.

---

### Pillar 5: Harness-Enforced Verification

#### Definition

Make completion conditional on objective evidence rather than the agent's confidence or self-reported success.

#### Why it matters

Verification closes the agentic loop. Without a test, build, visual comparison or other check, the only available completion signal may be that the result appears plausible to the model.

Effective verification should be:

- **Executable:** the agent or harness can run it
- **Objective:** the result has an unambiguous interpretation
- **Relevant:** it tests the intended behavior rather than only superficial validity
- **Enforced:** the agent cannot simply skip it and declare completion
- **Tamper-resistant:** the agent cannot satisfy the gate by weakening or deleting it
- **Independent when necessary:** the creator is not always the sole evaluator

Verification should operate in layers:

1. Static checks, formatting and type validation
2. Unit and integration tests
3. Runtime or browser validation
4. Independent agent review for targeted concerns
5. Human review for intent, architecture and maintainability
6. CI, deployment checks and production monitoring

#### Required capabilities

- A definition of done tied to executable checks
- Hooks, scripts or CI policies that can block completion
- Protected test suites and quality controls
- Fresh-context or separate-agent review for consequential work
- Evidence attached to the final handoff
- Maximum iteration, timeout or failure conditions

#### Failure modes

- Accepting “tests pass” without examining which tests ran
- Allowing the agent to remove failing tests
- Using the same model context to create and unquestioningly approve the change
- Equating green checks with architectural quality
- Relying on prose instructions instead of enforceable gates

#### Recommended organizational practice

Automate every deterministic expectation that can be automated. Reserve human review for the judgments that cannot be reduced to an executable pass or fail condition.

---

### Pillar 6: Single Accountable Coordination Authority

#### Definition

Maintain one authority responsible for prioritization, synthesis, acceptance and release.

#### Why it matters

Agentic workflows can include planners, implementers, researchers, test generators and reviewers. Without a coordinating authority, decisions can become fragmented and conflicting assumptions can be embedded across the resulting code.

A single authority does not mean one entity must perform all work. It means one entity owns the coherent outcome.

For consequential software engineering, the default authority should be a human who:

- Defines or approves the objective
- Reviews the implementation plan
- Resolves conflicting recommendations
- Assesses architectural consistency
- Accepts residual risks
- Approves release or merge

The agent can own an inner execution loop. The human owns the outer governance loop.

#### Required capabilities

- A clearly identified change owner
- Visible plans, decisions and unresolved risks
- Escalation paths for ambiguity or policy conflicts
- Synthesis of subagent outputs
- Explicit acceptance and release authority
- Review artifacts suitable for human judgment

#### Failure modes

- Treating the lead agent as the legal or organizational owner
- Allowing multiple agents to make incompatible architectural choices
- Delegating acceptance to the same system that implemented the change
- Generating work faster than the human authority can review it
- Unclear responsibility after a production failure

#### Recommended organizational practice

Assign one accountable human owner to each consequential agent-driven change. Design agent outputs to reduce that person's review burden rather than merely increasing generation throughput.

---

### Pillar 7: Isolated Execution with Conditional Concurrency

#### Definition

Use isolation broadly, but parallelize decomposed work only when task independence is genuine and integration is manageable.

#### Why it matters

Isolation and concurrency solve different problems.

**Isolation** provides:

- Protection from unrelated files, credentials and infrastructure
- Clean and reproducible workspaces
- Reduced cross-task interference
- Safer experimentation
- Easier comparison among competing approaches

**Concurrency** can provide throughput when:

- Tasks do not depend heavily on shared context
- Agents do not edit the same files or tightly coupled components
- Interfaces and output formats are explicit
- Integration ownership is clear
- Human review capacity is available

Parallelism is particularly suitable for:

- Independent research questions
- Competing hypotheses during debugging
- Redundant attempts at the same task
- Independent review dimensions
- Clearly separated modules with stable interfaces

It is less suitable for sequential work, same-file edits, architecture-heavy changes or tasks with many hidden dependencies.

#### Required capabilities

- Containers, worktrees or otherwise isolated workspaces
- Narrow credentials and permissions
- Clear ownership boundaries
- Structured outputs and handoffs
- A synthesis and integration step
- Conflict detection and review
- Cost and token visibility for parallel runs

#### Failure modes

- Conflicting decisions embedded in separate branches
- Merge conflicts and duplicated work
- Agents operating from inconsistent assumptions
- Parallel output overwhelming human review capacity
- Coordination overhead exceeding the speed benefit

#### Recommended organizational practice

Default to a single agentic thread for tightly coupled coding. Add concurrency only when the task graph demonstrates genuine independence or when redundant attempts provide useful comparison.

---

### Pillar 8: Observable, Economical and Bounded Operation

#### Definition

Expose execution evidence while constraining access, time, spend and operational risk.

#### Why it matters

Engineering teams need to know what the agent did, why it believes the task is complete, what evidence supports the result and what resources the process consumed.

Raw tool logs alone are not sufficient. Effective observability should produce reviewable engineering artifacts such as:

- Current plan and task status
- Files changed and diff summaries
- Commands executed
- Tests and validation results
- Failed attempts and recovery actions
- Permission requests and denials
- Screenshots or runtime evidence
- Remaining risks and assumptions
- Token usage, elapsed time and retries

The operating envelope should also be bounded through:

- Workspace-level write restrictions
- Network and credential controls
- Approval policies
- Maximum iterations and timeouts
- Token or financial budgets
- Deployment and destructive-action controls

#### Required capabilities

- Streaming progress or task status
- Durable execution and verification artifacts
- Permission telemetry and auditability
- Budget and timeout controls
- Recovery and resumability
- Outcome-based evaluation metrics
- Human-readable summaries linked to underlying evidence

#### Failure modes

- Reviewing agent confidence rather than evidence
- Unbounded loops consuming time and API budget
- Hidden permission escalation or credential access
- Measuring code volume rather than verified outcomes
- Producing so much execution detail that important risks are obscured

#### Recommended organizational practice

Evaluate the complete model-harness-environment configuration. Measure verified completion, regression rate, intervention count, review effort, permission escalations, recovery behavior, elapsed time and cost.

---

## 4. How the Pillars Work Together

The pillars form a connected delivery system rather than eight independent practices.

1. **Specification** defines the desired state and acceptance criteria.
2. **Unit-of-work design** converts the objective into tractable increments.
3. **Context engineering** supplies the information required for the current increment.
4. **Tool-enabled execution** allows the agent to inspect and change the real environment.
5. **The stateful loop** uses observed results to decide what to do next.
6. **Verification** determines whether the increment is complete.
7. **The coordinating authority** resolves ambiguity and accepts the integrated outcome.
8. **Isolation and conditional concurrency** control how work is separated and scaled.
9. **Observability and boundaries** make the process reviewable, affordable and safe.

Although expressed as eight pillars, the framework behaves as a cycle:

> **Specify → Decompose → Contextualize → Execute → Observe → Verify → Integrate → Review → Repeat**

The system is only as strong as its weakest control. Excellent tools cannot compensate for an unclear requirement. Strong tests cannot guarantee maintainability. Parallel agents cannot compensate for poor decomposition. Detailed instructions cannot enforce security boundaries. Human review cannot scale if the harness produces unstructured output faster than people can understand it.

---

## 5. Claude Code Implementation Assessment

### Overall Assessment

**Claude Code is a strong general-purpose coding harness with configuration-dependent assurance.**

It provides most of the primitives required by the eight-pillar framework:

- An iterative model and tool loop
- Repository-local instructions through `CLAUDE.md`
- Scoped rules, skills and auto memory
- Plan and Explore subagents
- File editing and shell execution
- MCP-based extensibility
- Fine-grained permissions
- Lifecycle hooks
- Custom subagents
- Experimental agent teams
- Session and context-management mechanisms

However, these primitives do not automatically produce a mature engineering operating model. Teams must still define how plans are persisted, which checks are mandatory, which actions are denied, how tests are protected, who owns acceptance and how costs and review capacity are managed.

A useful distinction is:

- **Instructions shape what Claude tries to do.**
- **Permissions and hooks constrain what Claude Code allows it to do.**
- **CI and human review determine whether the resulting change is acceptable to the organization.**

---

### Claude Code and Pillar 1: Stateful Reconciliation Loop

**Assessment: Strong native support with project-level configuration required**

Claude Code operates through iterative tool use. It can inspect files, run commands, read results, make edits and continue working from observed outcomes. This supports the core reconciliation loop.

Relevant capabilities include:

- File and repository inspection
- Shell command execution
- Tool results returned to the active conversation
- Plan-related workflows
- Session continuation and context compaction
- `CLAUDE.md` and auto memory for cross-session knowledge
- Task and subagent mechanisms for structured work

#### What is still missing or conditional

Claude Code does not guarantee that every project maintains a formal state ledger. A session can still lose track of the approved objective or infer completion from partial progress if the team has not established durable plan and handoff conventions.

#### Recommended implementation

For substantial work, require an explicit repository artifact such as:

- `PLAN.md`
- An issue or work-item checklist
- A progress or handoff document
- A structured task graph

The artifact should identify completed work, remaining work, failed approaches, acceptance criteria and unresolved risks.

---

### Claude Code and Pillar 2: Specification and Unit-of-Work Design

**Assessment: Supported, but strongly dependent on human and model judgment**

Claude Code supports read-only planning and repository exploration before implementation. Built-in Plan and Explore subagents can investigate a codebase without immediately modifying it. Custom subagents can also receive focused objectives, tools and output requirements.

These capabilities support:

- Separating discovery from implementation
- Delegating bounded analysis tasks
- Producing implementation plans
- Limiting tool access by role
- Keeping exploratory material out of the main implementation context

#### What is still missing or conditional

Claude Code cannot guarantee that a plan reflects the correct business requirement or that tasks have been decomposed along appropriate architectural boundaries. It can create a polished but flawed plan when the original intent is incomplete or when hidden dependencies are not discovered.

#### Recommended implementation

Require human approval for plans involving:

- Cross-cutting architecture changes
- Data migrations
- Authentication or security
- External integrations
- Production deployment
- Multiple modules or repositories

Each work unit should identify scope, dependencies, affected files or components, validation and rollback considerations.

---

### Claude Code and Pillar 3: Context and Knowledge Engineering

**Assessment: Strong native support**

Claude Code provides a layered context and memory model:

- `CLAUDE.md` files for persistent repository, user or organization guidance
- `.claude/rules/` for scoped instructions
- Skills for reusable multi-step procedures
- Auto memory for learned project patterns and user corrections
- Separate subagent context windows
- Compaction and context-management workflows

Anthropic's best-practice guidance treats the context window as a critical resource and recommends giving Claude focused context and executable feedback rather than loading everything indiscriminately.

#### What is still missing or conditional

`CLAUDE.md` and auto memory are contextual guidance rather than enforced configuration. They can become stale, redundant or contradictory. The model may also fail to apply an instruction consistently, especially in a crowded context.

#### Recommended implementation

Use a layered design:

- Put only universally relevant facts and commands in `CLAUDE.md`
- Put file-specific conventions in scoped rules
- Put lengthy procedures in skills
- Put security enforcement in permissions or hooks
- Store task-specific decisions in the active plan
- Review memory and instructions periodically for stale content

---

### Claude Code and Pillar 4: Tool-Enabled Execution Environment

**Assessment: Strong native support**

Claude Code can work directly with the development environment through file operations, shell commands and extensible tools. MCP integration can expose additional systems and services. Subagents can receive restricted or specialized tool sets.

This is a major advantage over chat-only coding assistance because Claude can compare its reasoning with actual repository and runtime evidence.

#### What is still missing or conditional

The reliability of this pillar depends on the environment provided by the team. Claude Code cannot compensate for broken setup scripts, inconsistent dependencies, unclear command output or unsafe third-party MCP servers.

#### Recommended implementation

- Use reproducible local or containerized environments
- Provide deterministic setup scripts
- Standardize build, test, lint and type-check commands
- Review MCP servers and tool permissions as trusted software
- Avoid exposing broad credentials when scoped credentials are sufficient
- Align the agent environment with CI as closely as practical

---

### Claude Code and Pillar 5: Harness-Enforced Verification

**Assessment: Strong mechanisms, but enforcement must be configured**

Claude Code can run tests, builds, linters, type checks and browser-based validation. Anthropic recommends giving Claude checks that produce an observable pass or fail signal so the agent can iterate until the result succeeds.

Hooks add an important enforcement layer. Claude Code lifecycle events can trigger commands or policies before tool use, after tool use or when Claude attempts to stop. This allows teams to block unsafe actions or prevent task completion until required checks pass.

#### What is still missing or conditional

Verification is not automatically independent, adversarial or tamper-resistant. Unless the team configures appropriate controls, the same agent can write the code, modify the tests and conclude that the result is complete.

#### Recommended implementation

- Use a `Stop` hook to require mandatory checks before completion
- Use `PreToolUse` hooks or permissions to protect test and policy files
- Run immutable CI checks outside the agent-controlled workspace
- Use a fresh-context review subagent for consequential changes
- Require the final report to identify commands run and actual results
- Keep human review for architecture, maintainability and intent

---

### Claude Code and Pillar 6: Single Accountable Coordination Authority

**Assessment: Supported as a workflow pattern, not guaranteed as governance**

A primary Claude Code session can coordinate subagents and synthesize their outputs. Experimental agent teams provide a lead-and-teammate model for more distributed work. A human can remain involved through plan review, permission approval, redirection and final code review.

#### What is still missing or conditional

Claude Code cannot assign organizational accountability. A lead agent can coordinate work, but it should not be treated as the final authority for accepting consequential software changes.

#### Recommended implementation

Designate one human change owner who:

- Approves the objective and plan
- Resolves architectural disagreements
- Reviews significant risks
- Owns final merge or deployment acceptance
- Ensures generated work receives appropriate review

Use the lead Claude session as an execution coordinator rather than as the ultimate accountable party.

---

### Claude Code and Pillar 7: Isolated Execution with Conditional Concurrency

**Assessment: Good support for delegation and emerging support for coordinated parallelism**

Claude Code subagents operate in separate context windows with their own prompts, tools and permissions. This helps isolate exploration, review or specialized work from the main conversation.

Agent teams support multiple Claude Code instances with a team lead, shared tasks and inter-agent communication. Anthropic describes agent teams as most useful when workers can operate independently and explicitly notes that they use more tokens and add coordination overhead.

#### What is still missing or conditional

Agent teams are experimental and have known limitations involving session resumption, task coordination and shutdown behavior. Separate contexts do not automatically provide separate filesystem workspaces. Parallel work can still create conflicts when agents edit the same files or make coupled architectural decisions.

#### Recommended implementation

- Use subagents for exploration, research, testing and focused review
- Use worktrees or containers when implementation work must be isolated at the filesystem level
- Use agent teams only for genuinely separable work
- Avoid parallel edits to the same file or tightly coupled subsystem
- Require one explicit synthesis and integration step
- Account for the additional token and review cost

---

### Claude Code and Pillar 8: Observable, Economical and Bounded Operation

**Assessment: Strong observability and permission controls, incomplete economic governance**

Claude Code exposes plans, diffs, command output, permission requests and session activity. Fine-grained rules can allow, ask about or deny tool use. Hooks can record lifecycle events or implement custom controls.

Permission enforcement is performed by Claude Code rather than being left solely to model compliance. This is essential because a prose instruction is not a security boundary.

#### What is still missing or conditional

Project-level economic governance generally requires external instrumentation. Token budgets, retry limits, review-capacity thresholds and outcome-based cost measurement are not automatically defined for each organization.

#### Recommended implementation

Track:

- Time to verified completion
- Test and acceptance pass rate
- Regression rate
- Human intervention count
- Human review effort
- Token or API usage
- Permission escalations and denials
- Failed command recovery
- Parallel-agent overhead
- Unresolved risks at handoff

Use permission rules, hooks, isolated environments and external budget controls to define the acceptable operating envelope.

---

## 6. Claude Code Gap Analysis

### 6.1 Specification quality

Claude Code can improve and structure a requirement, but it cannot independently establish whether the business objective is complete, internally consistent or aligned with stakeholder intent.

**Implication:** Human-led specification and acceptance remain first-class responsibilities.

### 6.2 Decomposition quality

Plan mode and subagents help divide work, but the hardest judgment is selecting boundaries that preserve architectural coherence.

**Implication:** Plans for complex work should be reviewed before implementation begins.

### 6.3 Independent acceptance

The model can run tests and review its own work, but creator and checker should be separated when consequences are material.

**Implication:** Use independent CI, fresh-context review and human approval.

### 6.4 Human review throughput

Autonomous generation can produce changes faster than trusted engineers can review them.

**Implication:** Optimize agent output for reviewability and limit parallelism based on review capacity.

### 6.5 Tamper-resistant controls

Hooks and permissions make strong controls possible, but the team must configure them. Instructions alone do not prevent an agent from weakening a gate.

**Implication:** Protect tests, policies and deployment controls outside the model's discretionary behavior.

### 6.6 Mature multi-agent coordination

Claude Code supports subagents and experimental agent teams, but parallel software work remains sensitive to shared context, dependencies and integration complexity.

**Implication:** Treat multi-agent coding as a conditional optimization rather than a default architecture.

### 6.7 Economic governance

More agents, longer loops and broader tool use increase token consumption, latency and review cost.

**Implication:** Measure total cost per verified outcome rather than only model usage or code-generation speed.

---

## 7. Recommended Claude Code Baseline

### Repository configuration

- Create a concise root `CLAUDE.md`
- Document architecture boundaries and source-of-truth locations
- Provide exact setup, build, test, lint and type-check commands
- Define prohibited operations and escalation requirements
- State the definition of done
- Move specialized procedures into skills
- Use scoped rules for component-specific conventions

### Planning and execution

- Require planning for ambiguous, cross-cutting or multi-hour work
- Persist substantial plans in the repository or work-tracking system
- Implement one coherent increment at a time
- Update progress after each verified increment
- Record failed approaches that a later session should avoid

### Verification

- Provide deterministic one-command checks
- Require tests before completion
- Use Stop hooks for mandatory validation
- Protect critical tests and controls
- Run independent CI outside the agent's control
- Use a separate reviewer for material changes
- Preserve final evidence in the handoff or pull request

### Security and permissions

- Use allow, ask and deny rules deliberately
- Deny access to unrelated secrets and infrastructure
- Require approval for network, deployment and destructive actions
- Use isolated workspaces for high-autonomy execution
- Review MCP integrations and custom hooks as part of the trusted computing base

### Delegation and parallelism

- Use Explore or custom subagents to keep research out of the main context
- Use separate review agents for security, tests or architecture where valuable
- Use worktrees or containers for parallel implementation
- Avoid parallel work with high shared-state or same-file dependency
- Keep one synthesis owner
- Enable agent teams only for clear independent work and with explicit cost awareness

### Governance and measurement

- Assign a human change owner
- Review plans before expensive execution
- Track verified outcomes rather than lines of generated code
- Measure intervention count, review effort, regressions, cost and elapsed time
- Maintain an audit trail of commands, permissions and validation
- Set limits for iterations, runtime and spend

---

## 8. Evaluation Criteria for Agentic Coding Systems

A robust internal benchmark should evaluate the full model-harness-environment configuration across the following dimensions.

### Outcome quality

- Task completion rate
- Acceptance-check pass rate
- Regression rate
- Severity of escaped defects
- Architectural and maintainability review outcomes

### Execution quality

- Time to verified completion
- Recovery from failed commands
- Number of repeated or unnecessary actions
- Accuracy of plans and task status
- Traceability from requirement to evidence

### Human burden

- Intervention count
- Review time
- Number of clarification cycles
- Cognitive load of generated artifacts
- Ease of understanding and modifying the result

### Safety and governance

- Permission escalations
- Denied or blocked actions
- Credential and network exposure
- Destructive-action attempts
- Compliance with protected-path and policy controls

### Economics

- Token and API cost
- Compute or sandbox cost
- Parallel-agent overhead
- Cost per verified task
- Cost of human review and rework

### Continuity and observability

- Quality of plans and handoffs
- Session resumption success
- Completeness of test and command evidence
- Accuracy of final summaries
- Ability to reconstruct why a change was made

---

## 9. Conclusion

Agentic software engineering is not simply conventional development performed faster by a language model. It is a distinct systems-engineering discipline centered on the harness, execution environment, context strategy, verification architecture and human operating model surrounding the model.

The eight pillars provide a practical structure for that discipline:

1. The **stateful reconciliation loop** keeps reasoning grounded in desired and observed state.
2. **Specification and unit-of-work design** make substantial work tractable.
3. **Context and knowledge engineering** preserve the right information without overwhelming the active context.
4. The **tool-enabled execution environment** connects reasoning to real software systems.
5. **Harness-enforced verification** makes completion evidence-based.
6. A **single accountable authority** preserves coherence and ownership.
7. **Isolation with conditional concurrency** enables safe scaling without assuming every task should be parallelized.
8. **Observable, economical and bounded operation** makes autonomy governable in a real organization.

Claude Code implements a strong set of primitives across all eight pillars. Its effective use, however, depends on repository design and organizational discipline. A minimally configured Claude Code session is an agentic coding tool. A well-designed combination of instructions, skills, environments, permissions, hooks, tests, CI and human ownership can become an agentic software-engineering system.

The practical objective is not maximum autonomy. It is **the highest level of autonomy that remains verifiable, reviewable, economical and accountable**.

---

## References

### Foundation reports

- *Harness Engineering for Frontier Coding Agents*. July 15, 2026.
- *Verifying a Five-Pillar Framework for Agentic Software Engineering*. July 15, 2026.

### Claude Code documentation

- Anthropic. [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices).
- Anthropic. [How Claude remembers your project](https://code.claude.com/docs/en/memory).
- Anthropic. [Configure permissions](https://code.claude.com/docs/en/permissions).
- Anthropic. [Hooks reference](https://code.claude.com/docs/en/hooks).
- Anthropic. [Create custom subagents](https://code.claude.com/docs/en/sub-agents).
- Anthropic. [Orchestrate teams of Claude Code sessions](https://code.claude.com/docs/en/agent-teams).

### Supporting Anthropic engineering research

- Anthropic. [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents).
- Anthropic. [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents).
- Anthropic. [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents).
- Anthropic. [Building agents with the Claude Agent SDK](https://claude.com/blog/building-agents-with-the-claude-agent-sdk).
- Anthropic. [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system).

### Practitioner and industry perspectives represented in the foundation research

- Addy Osmani, writings on loop engineering, autonomy, code review and long-running agents
- Mitchell Hashimoto, writings and interviews on practical agentic engineering workflows
- Simon Willison, writings on agent definitions, tool loops and verification
- Cognition, *Don't Build Multi-Agents*
- Cursor, *Best practices for coding with agents*
- Thorsten Ball, *How to Build an Agent*
- Steve Yegge, writings and the Beads task-management project
- Manus, *Context Engineering for AI Agents: Lessons from Building Manus*
