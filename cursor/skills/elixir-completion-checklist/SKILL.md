---
name: elixir-completion-checklist
description: Runs a standard Elixir/Phoenix completion checklist (mix format, compile, test, dialyzer) via a shell subagent. Use before marking an Elixir task complete, when asked for a completion checklist, or before pushing code for review.
---

# Elixir completion checklist

Run this checklist before marking any Elixir/Phoenix work complete in the target project.

## Prerequisites

- Set **`ELIXIR_PROJECT_ROOT`** to the absolute path of the Mix project (the directory that contains `mix.exs`).

## Steps

Launch a `shell` subagent with `model: "fast"`, `working_directory` set to `ELIXIR_PROJECT_ROOT`, and the following prompt (substitute the path in both places):

```
Run the Elixir completion checklist sequentially in <ELIXIR_PROJECT_ROOT>.
Stop and report immediately if any step fails.

Steps:
1. mix format
2. mix compile
3. mix test
4. mix dialyzer

For each step, report: PASS or FAIL, and include any error output on failure.
Return a summary of all results at the end.
```

## On results

- If any step fails, fix the reported errors before proceeding.
- Do not mark a task complete until all four steps pass (or your project has explicitly documented a different gate).
- `mix dialyzer` may take several minutes on first run while building the PLT — that is expected.

## Project-specific overrides

Teams may add steps (e.g. `mix compile --warnings-as-errors`, `mix credo`) or skip dialyzer in CI-only flows. When this skill is copied into a repo, document those differences in the skill body or in `README`.
