---
name: submit-pr-github-bugbot
description: Create a GitHub pull request, wait for automated review (e.g. Cursor Bugbot), triage comments, apply safe fixes in a loop, and re-trigger review until checks pass. Use when opening a PR, after pushing a feature branch, or when asked to submit a PR for review.
---

# Submit PR (GitHub + automated review)

Creates a PR, monitors Bot/reviewer comments, fixes what is safe without product decisions, and loops until clean.

Set these before following the commands:

| Variable | Example | Used for |
|----------|---------|----------|
| `GITHUB_OWNER` | `my-org` | `gh` and API URLs |
| `GITHUB_REPO` | `my-app` | `gh` and API URLs |
| `WORKTREE_PATH` | absolute path to clone | `cd` before `gh` / git |
| `COMPLETION_CHECKLIST` | optional skill name | e.g. run `elixir-completion-checklist` before each push |

## Workflow

### Step 1 — Create the PR

If not already created:

```bash
export PATH="$HOME/.local/bin:$PATH"
cd "$WORKTREE_PATH"
gh pr create --repo "$GITHUB_OWNER/$GITHUB_REPO" --title "<title>" --body "<body>"
```

Capture the PR number from the output URL.

### Step 2 — Wait for automated review

If your org uses Cursor Bugbot (or similar), it often comments on the PR shortly after open. Poll until a bot comment appears or a timeout passes (e.g. 5 minutes), then proceed.

```bash
gh pr view <number> --repo "$GITHUB_OWNER/$GITHUB_REPO" --comments 2>&1 | grep -i "bugbot\|cursor bugbot"
```

If nothing appears, continue — automation may be disabled or slow.

### Step 3 — Collect comments and CI

```bash
# PR-level (issue) comments — many bots post here
gh api "repos/$GITHUB_OWNER/$GITHUB_REPO/issues/<number>/comments" \
  --jq '.[] | {author: .user.login, body: .body, id: .id}'

# Inline review comments
gh api "repos/$GITHUB_OWNER/$GITHUB_REPO/pulls/<number>/comments" \
  --jq '.[] | {author: .user.login, body: .body, path: .path, line: .original_line}'

# CI checks
gh pr checks <number> --repo "$GITHUB_OWNER/$GITHUB_REPO"
```

### Step 4 — Triage

For each comment, classify:

**Fix automatically** (no product owner needed):

- Formatting and style fixes required by the project
- Missing docs/typespecs if your standards require them
- Obvious bugs with one clear fix
- Typos in user-visible strings or docs

**Ask the product owner / team first**:

- Architecture or API shape changes
- Ambiguous business rules
- Security-sensitive changes
- Anything flagged **High Risk** by automated review

Batch all “ask first” items in **one** message when possible.

### Step 5 — Apply fixes

Use your project’s completion checklist (tests, lint, typecheck) after edits. For Elixir projects, the `elixir-completion-checklist` skill in this repo is a common pattern.

### Step 6 — Commit, push, re-trigger bot

```bash
cd "$WORKTREE_PATH"
git add -A
git commit -m "<concise description of fixes>"
git push

# If your bot supports a re-run trigger (example for Bugbot):
gh pr comment <number> --repo "$GITHUB_OWNER/$GITHUB_REPO" --body "bugbot run"
```

### Step 7 — Loop

Repeat from Step 2 until there are no new bot findings, review threads are resolved, and `gh pr checks` is green.

## Severity heuristic (automation labels)

| Label | Suggested action |
|-------|------------------|
| Low | Fix when trivial; skip nitpicks if allowed |
| Medium | Fix clear issues; ask when ambiguous |
| High | Confirm with a human before changing behavior |

## Notes

- Ensure `gh` is on `PATH` (common location: `~/.local/bin/gh`).
- Replace API examples if you use GitHub Enterprise Host (`GH_HOST` / `gh auth`).
- Stack-specific steps (e.g. `mix format`, `npm test`) belong in the project’s checklist skill, not hardcoded here.
