---
name: elixir-completion-checklist
description: "Run the standard Elixir/Phoenix completion checklist (mix format, compile, test, dialyzer). Use before marking an Elixir task complete, after a rebase, for mid-development sanity checks, or before pushing code for review."
metadata:
  {
    "openclaw": {
      "emoji": "✅",
      "requires": { "anyBins": ["mix"] }
    }
  }
---

# Elixir Completion Checklist

Run this checklist before marking any Elixir/Phoenix work complete.

## Steps

Run sequentially in the project root (the directory containing `mix.exs`). Stop and fix immediately if any step fails.

```bash
mix format
mix compile --warnings-as-errors
mix test
mix dialyzer
```

## Rules

- **All four must pass** with zero warnings and zero failures before proceeding.
- Do not mark a task complete, commit, or push until the checklist is clean.
- `mix dialyzer` may take several minutes on first run while building the PLT — that is expected.

## Per-step guidance

| Step | What it catches |
|------|-----------------|
| `mix format` | Inconsistent formatting — always run first so diffs are clean |
| `mix compile --warnings-as-errors` | Unused variables, missing modules, type mismatches at compile time |
| `mix test` | Regressions, broken contracts, missing coverage |
| `mix dialyzer` | Type violations, unreachable code, spec mismatches |

## Project-specific overrides

Teams may add steps or adjust flags. Common additions:
- `mix compile --warnings-as-errors` (already included above)
- `mix credo --strict` for style/consistency linting
- `mix coveralls` for coverage thresholds

When this skill is copied into a project, document overrides in the skill body or in the project's `CLAUDE.md`.

## When to use

- **Before every commit** (mandatory)
- **After a rebase** — catch conflicts that compiled but broke tests
- **Mid-development** — sanity check before continuing a large change
- **Before PR submission** — the `pr-submit` skill calls this implicitly, but running it early avoids surprises
