---
name: git-worktree-dev-environment
description: "Set up a dedicated git worktree for isolated parallel development. Use when starting a new feature branch, setting up a second workspace, avoiding branch checkout conflicts, or when multiple agents need to work on different branches simultaneously."
metadata:
  {
    "openclaw": {
      "emoji": "🌳"
    }
  }
---

# Git Worktree Dev Environment

Isolates work using **git worktrees**: separate directories that share the same `.git` object store but can check out different branches without conflicts.

## When to use

- Starting a new feature while another branch is in progress
- Multiple agents working on different issues simultaneously
- Avoiding `git stash` / branch switching in the main clone
- Clean environment for PR review or testing

## Setup

### 1. Create the worktree

From the **main clone**, fetch latest and create:

```bash
cd <MAIN_REPO_PATH>
git fetch origin
git worktree add -b <BRANCH_NAME> <WORKTREE_PATH> origin/main
```

Conventions:
- **Path:** `/tmp/<repo>-<slug>` or sibling folder `../<repo>-<slug>/`
- **Branch:** `feature/issue-{N}-{description}` or `fix/issue-{N}-{description}`

### 2. Install dependencies

Dependencies are **not shared** across worktrees. Run your project's install in the new directory:

```bash
cd <WORKTREE_PATH>
# Elixir: mix deps.get && mix ecto.setup
# Node:   npm install
# Python: pip install -r requirements.txt
```

### 3. Isolate local resources

Each worktree needs its own ports and database to avoid collisions:

| Concern | How to isolate |
|---------|----------------|
| HTTP port | Increment from main dev port (e.g. main `4000`, worktree `4001`) |
| Database | Separate DB name per worktree (e.g. `myapp_dev_issue42`) |
| Env file | Copy `.env.example` → `.env`; override port/DB — **never commit secrets** |

Set these via environment variables or a local `.env` file before starting the dev server.

### 4. Run migrations and seeds

```bash
cd <WORKTREE_PATH>
# Elixir:  mix ecto.create && mix ecto.migrate && mix run priv/repo/dev_seeds.exs
# Rails:   rails db:create db:migrate db:seed
# Django:  python manage.py migrate
```

## Port allocation

Pick a range (e.g. `4001–4010`) and assign sequentially. Check for conflicts:

```bash
# macOS / Linux
lsof -i -P | grep -E ':400[0-9] .*(LISTEN)' || echo "no conflicts"
```

## Shared infrastructure

If a shared Postgres (or Docker Compose stack) serves all worktrees:

- Start containers from **one** directory (usually the primary clone)
- Give each worktree a **distinct database name** so migrations and seeds don't fight
- Don't run the same Compose project from multiple paths — it duplicates containers

## Teardown

After merge or when abandoning work:

```bash
# Remove the worktree
git worktree remove <WORKTREE_PATH>

# Delete the local branch (if merged)
git branch -D <BRANCH_NAME>

# Optionally delete the remote branch
git push origin --delete <BRANCH_NAME>

# Drop the isolated database (if created)
# Elixir: MIX_ENV=dev mix ecto.drop  (from the worktree before removing it)
# Or:     dropdb <db_name>
```

Use `git worktree list` to see all registered worktrees and clean up stale ones.

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Wrong DB or port | Ensure the shell session sources the worktree `.env` before starting |
| Missing node_modules / _build | Run dependency install in the worktree — artifacts aren't shared |
| Migration conflicts | Resolve on the integration branch first, then rebase the worktree branch |
| "fatal: is already checked out" | Another worktree has the branch — use `git worktree list` to find it |
