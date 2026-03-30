# Cursor rules — planning repository (generic template)

Copy this file to your **planning/documentation** repository root as `.cursorrules` (or merge sections into an existing file). Replace every `{{PLACEHOLDER}}`. Remove this title block if you prefer a project-specific H1.

---

## Overview

This workspace holds **{{PLANNING_SCOPE_DESCRIPTION}}** — for example architecture, requirements, and product-level references. **Implementation code** lives in **`{{IMPLEMENTATION_REPO_NAME}}`**. **Implementation plans** for that codebase live under **`{{IMPLEMENTATION_REPO_NAME}}/implementation_plans/`** at the implementation repository root unless your team documents otherwise.

## Implementation plans

### Location and naming

- Store plans in **`{{IMPLEMENTATION_REPO_NAME}}/implementation_plans/`** (common convention) beside the application code.
- For work tied to a GitHub issue, use **`issue-{NUMBER}-{short-description}.md`**.
- Follow any extra rules in **`{{IMPLEMENTATION_REPO_NAME}}/implementation_plans/README.md`**.

### Plan template

```markdown
# [Feature/Fix Name]

> **Architecture:** {{PATH_TO_ARCHITECTURE_DOC}}#[section]
> **Requirements:** {{PATH_TO_REQUIREMENTS_DOC}}#[section]
> **Issue:** {{ISSUE_URL_FOR_ISSUE_N}}

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

1. **Break down features** into discrete, implementable issues or deliverables.
2. **Identify dependencies** between them.
3. **Reference architecture sections** that drive the design.
4. **Include technical detail** from architecture (data models, APIs, integrations).
5. **Follow your team’s plan format** (e.g. `implementation_plans/README.md`) for consistency.

### Plan updates

- Treat plans as **living documents**; update as implementation progresses.
- Add **Implementation notes** (or similar) when execution diverges from the plan.
- Keep the plan file **next to or clearly linked to** the codebase it describes, per team convention.

## GitHub issues (typical workflow)

1. Create an issue on **`{{GITHUB_OWNER}}/{{GITHUB_IMPLEMENTATION_REPO}}`**.
2. Add **`implementation_plans/issue-{NUMBER}-{description}.md`** (or your naming convention).
3. Link the plan in the issue description.
4. Update the plan as work proceeds.
5. Close the issue when acceptance criteria are met.

## Architecture documentation layout

- **Root:** A **high-level architecture** document (name may vary) captures version-independent system design.
- **Version folders:** Use **semantic versioning** in folder names (e.g. `v0.1.0`, `v0.2.0`). Optional display names in parentheses are fine.
- **Contents:** Version folders may hold wireframes, design notes, feature specs, and other material scoped to that release.

Adjust directory names to match your repository; keep a single obvious entry point for “current” vs “historical” architecture when helpful.

## Directory structure (examples — adjust names)

**Planning repository `{{PLANNING_REPO_NAME}}`:**

```
{{PLANNING_REPO_NAME}}/
├── {{ARCHITECTURE_DIR}}/          # e.g. architecture/
├── {{REQUIREMENTS_DIR}}/          # e.g. requirements/
└── {{OTHER_DOC_DIRS}}/
```

**Implementation repository `{{IMPLEMENTATION_REPO_NAME}}`:**

```
{{IMPLEMENTATION_REPO_NAME}}/
├── implementation_plans/
├── {{SOURCE_ROOT}}/               # e.g. lib/, src/, app/
└── ...
```

## Referencing code in plans

When implementation plans mention code:

- Use **module / package / namespace** names as your stack expects (e.g. `MyApp.Context.Module`).
- Use **paths relative to the implementation repository root**.
- Include **short code excerpts** when they clarify behavior or contracts.
- Call out **framework or team conventions** and any **intentional deviations**.

## Referencing other documents

Plans should point to:

- **Architecture** — stable URLs or paths (including cross-repository links if docs live separately).
- **Requirements** — same as above.
- **Other implementation plans** — use **relative paths within the implementation repo** when plans sit beside code; otherwise document the canonical location.

Use a consistent style for cross-repo links (e.g. `planning-repo/architecture/...` vs full GitHub URLs) so agents and humans can resolve them.

## Best practices for implementation plans

1. **Be specific** — Enough detail to execute without guessing.
2. **Identify dependencies** — Preconditions must be explicit.
3. **Include examples** — Data shapes, API sketches, or pseudocode where helpful.
4. **Document decisions** — Brief rationale for non-obvious choices.
5. **Keep plans current** — Update when scope or understanding changes.

## Multi-tenancy (when applicable)

If the product is multi-tenant, plans should consider:

- Tenant isolation requirements.
- Tenant scoping on identifiers (e.g. `tenant_id` in queries and APIs).
- Row-level security or equivalent enforcement, if used.
- Test scenarios that prove isolation.

## Security

Plans should address where relevant:

- Credential storage and rotation.
- API and transport security.
- Tenant or customer data boundaries.
- Input validation and safe defaults.
- Errors and logs that **do not** leak sensitive data.

## Performance

Plans should consider where relevant:

- Database indexes and query shape.
- Caching and invalidation.
- Background or async processing.
- Rate limits and backoff for external services.
