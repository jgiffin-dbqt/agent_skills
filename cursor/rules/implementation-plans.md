## Implementation plans

### Location and naming

- Store implementation plans where your team has decided they live—commonly **`{{IMPLEMENTATION_REPO_NAME}}/implementation_plans/`** at the **implementation** repository root, beside application code.
- When a plan maps to a GitHub issue, prefer filenames: **`issue-{NUMBER}-{short-description}.md`**.
- Follow any additional naming rules in **`{{IMPLEMENTATION_REPO_NAME}}/implementation_plans/README.md`** (or equivalent).

### Plan template

Plans should tie to product/architecture docs and to the tracking issue when one exists. Adapt paths to your repos (planning monorepo vs single docs repo).

```markdown
# [Feature/Fix Name]

> **Architecture:** {{PATH_TO_ARCHITECTURE_DOC}}#[section]
> **Requirements:** {{PATH_TO_REQUIREMENTS_DOC}}#[section]
> **Issue:** {{ISSUE_TRACKER_URL_FOR_ISSUE_N}}

## Overview

What this plan covers and why.

## Current State

What exists today.

## Implementation

Step-by-step approach with file paths and acceptance criteria.

## Testing

What tests will be added or modified.

## Acceptance Criteria

How we know this is done.
```

### Creating plans from architecture

When generating plans from architecture or requirements:

1. **Break down features** into discrete, implementable issues or deliverables.
2. **Identify dependencies** between them.
3. **Reference architecture sections** that drive the design.
4. **Include technical detail** from architecture (data models, APIs, integrations).
5. **Follow your team’s plan format** (e.g. `implementation_plans/README.md`) for consistency.

### Plan updates

- Treat plans as **living documents**; update them as implementation progresses.
- Add an **Implementation notes** (or similar) section when reality diverges from the plan.
- Keep the plan file **next to or clearly linked to** the codebase it describes, per team convention.

## GitHub issues (typical workflow)

1. Create an issue with title and description on **`{{GITHUB_OWNER}}/{{GITHUB_REPO}}`** (usually the implementation repo).
2. Add **`implementation_plans/issue-{NUMBER}-{description}.md`** (or your naming convention).
3. Link the plan in the issue description.
4. Update the plan as work proceeds.
5. Close the issue when the work meets acceptance criteria.
