# azure

Azure DevOps interaction via the `az` CLI. Read-only by default.

## Skills

- **`az-devops`** — query and browse work items (epics, features, stories, tasks, bugs), view sprint boards, drill into parent-child hierarchies, list Git repositories.

## When to invoke

User mentions Azure DevOps, ADO, work items, sprints, epics, stories, boards, repos, pipelines, iterations, or queries. Vague triggers like "what's in my sprint?" or "show me that ticket" also apply.

## Tool boundaries

- Uses the `az` CLI (specifically `az boards` and `az repos`). Assumes the user is already authenticated via `az login` and has a default ADO organization/project set, or supplies them inline.
- Read-only by default. Any state-changing command (work item creation, repo writes) must be confirmed explicitly by the user.

## Conventions

- Output: markdown tables for lists, structured bullets for single-item drill-downs. No filler prose.
- Never fabricate work item IDs, titles, or assignees. If a query returns nothing, say so.
