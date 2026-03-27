---
name: git-worktree-dev-environment
description: Use a dedicated git worktree per agent or task with an isolated working directory, branch, and local resources (ports, databases) so parallel work does not collide. Use when setting up a second workspace, avoiding branch checkout conflicts, or cleaning up after a merged or abandoned branch.
---

# Git worktree dev environment

Isolates work using **git worktrees**: separate directories that share the same `.git` object store but can checkout different branches.

## What to configure per worktree

Adapt names to your project:

| Concern | Typical pattern |
|---------|------------------|
| Directory | Sibling folder: `../<repo>-<slug>/` or a team convention |
| Branch | `feature/<slug>` or your naming standard |
| HTTP port | Increment from the main dev port (e.g. main app `4000`, worktrees `4001`, `4002`, …) |
| Database | Separate logical DB name per worktree if the app uses a local DB |
| Env file | Copy `.env.example` → `.env`; append overrides for port/DB — **never commit secrets** |

## Setup (conceptual)

1. From the **main clone**, fetch latest default branch (e.g. `main`).
2. `git worktree add -b feature/<slug> <path> origin/main` (or equivalent).
3. In the worktree: install dependencies, run migrations, set env vars for port and DB.
4. Tell the agent: **working directory**, **branch**, **port**, **DB name** (if relevant).

Many teams wrap this in a script checked into the **application** repo (not this generic repo) because paths, DB tooling, and seeds are project-specific.

## Teardown

After merge or when abandoning work:

```bash
git worktree remove <path>
# Optionally delete the remote branch after merge:
# git push origin --delete feature/<slug>
```

Use `git worktree list` to see registered worktrees.

## Shared infrastructure

If one Docker Compose stack (or shared Postgres) serves all local worktrees:

- Start containers from **one** designated directory (usually the primary clone).
- Give each worktree a **distinct** database name or schema so migrations and seeds do not fight.
- Avoid running the same Compose project twice from different paths if that would duplicate container names — use your stack’s documented pattern.

## Port allocation

Pick a range (e.g. `4001–4010`) and assign sequentially. Check listeners before assigning:

```bash
# Linux / macOS — adjust port range
ss -tlnp | grep -E ':400[0-9] ' || true
```

## Troubleshooting (generic)

- **Wrong DB or port**: Ensure the shell session `source`s the worktree `.env` before `mix`, `rails`, `npm`, etc.
- **Missing node modules / build artifacts**: Dependencies are usually **not** shared across worktrees; run install in each tree.
- **Migration conflicts**: Resolve on the integration branch first, then rebase or merge into the worktree branch.

Project-specific fixes (Tailwind, seeds, vendor credentials) belong in that repo’s docs or a non-generic skill.
