---
name: submit-pr-github-self-review
description: Create a GitHub pull request, run repeated cold self-reviews via a read-only subagent given only the PR URL or number, fix issues, and loop until the review is clean and CI passes. Use when opening a PR, after pushing a feature branch, or when asked to submit a PR for review.
---

# Submit PR (GitHub + cold self-review)

Creates a PR, then **you** (the orchestrating agent) iterate: spawn a **cold** review subagent with **no context except the PR reference**, address findings, push, and repeat until the self-review is clean and `gh pr checks` is green.

Set these before following the commands:

| Variable | Example | Used for |
|----------|---------|----------|
| `GITHUB_OWNER` | `my-org` | `gh` and API URLs |
| `GITHUB_REPO` | `my-app` | `gh` and API URLs |
| `WORKTREE_PATH` | absolute path to clone | `cd` before `gh` / git |
| `COMPLETION_CHECKLIST` | optional skill name | e.g. run your project’s completion checklist before each push |

## Workflow

### Step 1 — Create the PR

If not already created:

```bash
export PATH="$HOME/.local/bin:$PATH"
cd "$WORKTREE_PATH"
gh pr create --repo "$GITHUB_OWNER/$GITHUB_REPO" --title "<title>" --body "<body>"
```

Capture the PR number or URL from the output.

**PR reference for subagents:** Prefer a single canonical line you can paste, e.g. `https://github.com/$GITHUB_OWNER/$GITHUB_REPO/pull/<number>`. If you only pass the number to the subagent, expand it to that full URL first so the subagent still receives one self-contained reference (the subagent prompt must not rely on separate repo context).

### Step 2 — Cold self-review (subagent)

Launch a **read-only** subagent (`subagent_type`: `generalPurpose`, `readonly`: true). **Prompt content rule:** the subagent must receive **only** the PR link **or** a single equivalent reference line (full URL or `owner/repo#number`). Do not paste your planning notes, issue bodies, or local file excerpts into the review task.

Suggested subagent brief (replace `<PR_REF>`):

```
You are doing a cold code review. You have no workspace or chat context beyond this line:
<PR_REF>

Use gh (and GitHub as needed) to inspect the PR: scope, diff, checks status. Report bugs, security issues, test gaps, contract/API risks, and consistency problems. Classify each item: must-fix, should-fix, or nit. If there are no must-fix or should-fix items and checks are acceptable, state clearly that the review is clean.
```

The subagent may run shell commands to fetch the PR; the **orchestrator** applies code changes, not the reviewer.

### Step 3 — Triage (you)

For each finding from the self-review:

**Fix without escalation**

- Formatting and style fixes required by the project
- Missing docs/typespecs if standards require them
- Obvious bugs with one clear fix
- Typos in user-visible strings or docs

**Ask the product owner / team first**

- Architecture or API shape changes
- Ambiguous business rules
- Security-sensitive behavior changes
- Anything that would materially change product intent

Batch all “ask first” items in **one** message when possible.

### Step 4 — Apply fixes

Run your project’s completion checklist (tests, lint, typecheck) after edits.

### Step 5 — Commit and push

```bash
cd "$WORKTREE_PATH"
git add -A
git commit -m "<concise description of fixes>"
git push
```

### Step 6 — Verify CI

```bash
gh pr checks <number> --repo "$GITHUB_OWNER/$GITHUB_REPO"
```

Resolve failing checks before the next review pass.

### Step 7 — Loop

Repeat **Step 2** through **Step 6** until:

1. The cold subagent reports the review as **clean** (no must-fix or should-fix items you agree with), and  
2. `gh pr checks` is green (or only allowed flaky/suppressed checks per team policy).

Stale findings after a push are expected; each review pass is cold against the current PR state.

## Classifying subagent findings

| Severity | Suggested action |
|----------|------------------|
| Must-fix | Resolve before merging; ask if the fix implies a product decision |
| Should-fix | Resolve when clear; skip only with documented rationale |
| Nit | Fix when trivial; otherwise note for follow-up |

## Notes

- Ensure `gh` is on `PATH` (common location: `~/.local/bin/gh`).
- Replace workflows if you use GitHub Enterprise Server (`GH_HOST` / `gh auth`).
- Stack-specific gates (e.g. `mix format`, `npm test`) belong in the project’s checklist skill, not here.
- Human reviewers may still comment; fold their feedback into the same triage/fix/self-review loop if desired.
