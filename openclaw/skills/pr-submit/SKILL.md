---
name: pr-submit
description: "Submit PRs with mandatory self-review, screenshots, and quality checks. Use for EVERY PR submission — no exceptions. Covers the full workflow: quality checks → push → create PR → screenshots (if UI changed) → cold self-review → fix findings → re-review until clean → notify. Read this skill EVERY TIME you submit a PR."
metadata:
  {
    "openclaw": {
      "emoji": "🚀",
      "requires": { "anyBins": ["gh"] }
    }
  }
---

# PR Submit

**Read this skill EVERY TIME you submit a PR. No shortcuts.**

## The Full Workflow

```
1. Quality checks (project-specific: lint, compile, test, typecheck)
2. Commit and push
3. Create PR via gh
4. Screenshots (if ANY UI/template file changed)
5. Cold self-review (spawn fresh sub-agent)
6. Fix all findings
7. Re-review until clean
8. Notify human
```

Every step is mandatory. A PR missing any step is NOT ready.

## Step 1: Quality Checks

Run your project's full check suite before committing. All must pass with zero warnings/failures.

Examples by stack:
- **Elixir/Phoenix:** `mix format && mix compile --warnings-as-errors && mix test && mix dialyzer`
- **Node/TypeScript:** `npm run lint && npm run typecheck && npm test`
- **Python:** `ruff check . && mypy . && pytest`

Adapt to your project. The point is: **every automated check passes before you push.**

## Step 2: Commit and Push

```bash
git add -A
git commit -m "type: description (#issue)"
git push -u origin branch-name
```

Commit message format: `fix:`, `feat:`, `docs:`, `test:`, `chore:` prefix.
Reference the issue number when applicable.

## Step 3: Create PR

```bash
gh pr create -R <OWNER>/<REPO> \
  --head branch-name \
  --title "type: description (#issue)" \
  --body "## Summary
...
Closes #N"
```

## Step 4: Screenshots (if UI changed)

**Trigger:** PR touches ANY template, view component, CSS/styling, or JS that affects rendering.

Use the `pr-screenshots` skill. Key steps:
1. Start dev server on the PR branch
2. Capture every changed view with playwright
3. Post screenshots as a PR comment with labels

**A PR that changes UI without screenshots is NOT ready.**

## Step 5: Cold Self-Review

Spawn a **fresh sub-agent** that has NO knowledge of what you were trying to build.
It gets only the PR diff and repo context.

The cold reviewer:
- Gets ONLY the PR reference (URL or `owner/repo#number`)
- Has no knowledge of intent or what you were building
- Fetches the diff via `gh pr diff`
- Posts a structured review comment on the PR

### Review comment format:

```markdown
## Cold Review: PR #N
### Summary
### Findings (🔴 High / 🟡 Medium / 🟢 Low / 💡 Suggestion)
### Verdict (✅ Approve / ⚠️ Approve with suggestions / ❌ Request changes)
```

**Rules:**
- A PR with no review comment is NOT ready — period
- The review comment is proof the process ran

## Step 6: Fix All Findings

- 🔴 High: MUST fix before merge
- 🟡 Medium: MUST fix before merge
- 🟢 Low: Fix if reasonable, otherwise document why skipped
- 💡 Suggestion: Optional, use judgment

After fixing, run quality checks again (Step 1).

## Step 7: Re-Review

After pushing fixes, spawn ANOTHER cold reviewer on the new changes.
Repeat Steps 5-6 until the reviewer returns ✅ Approve with no
High/Medium findings.

**The re-review is not optional.** Fixes can introduce new issues.

## Step 8: Notify

Only after all of the above:
- Review comment posted and clean
- Screenshots posted (if UI changed)
- All quality checks passing

Tell the human: "PR #N is ready for your review."

## Checklist (copy-paste for self-check)

```
- [ ] Quality checks pass (zero warnings/failures)
- [ ] PR created with clear description
- [ ] Screenshots posted (if UI changed)
- [ ] Cold self-review posted on PR
- [ ] All High/Medium findings fixed
- [ ] Re-review clean after fixes
- [ ] Human notified
```

## Common Mistakes

1. **Skipping screenshots** on UI changes
2. **Marking PR as ready without review comment**
3. **Not re-reviewing after fixes** — fixes can introduce new issues
4. **Running cold reviewer with knowledge of intent** — defeats the purpose
5. **Fixing only High findings** — Medium findings are also mandatory fixes

## Clean Up

After PR is merged:
```bash
# Remove worktree (if used)
git worktree remove /tmp/worktree-dir

# Delete local branch
git branch -D branch-name

# Pull main
git pull
```
