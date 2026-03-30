# Claude Code — shared config and templates

Generic `CLAUDE.md` templates and settings for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Project-specific details (repo names, contexts, credentials) stay in each project repo; use these as starting points.

## How Claude Code discovers instructions

Claude Code reads instructions from these locations (in order of precedence):

1. **`CLAUDE.md`** at the repo root — project-level instructions (committed, shared with team)
2. **`~/.claude/CLAUDE.md`** — user-level global instructions (personal, not committed)
3. **`~/.claude/settings.json`** — global settings (permissions, etc.)
4. **`~/.claude/settings.local.json`** — local overrides (machine-specific permissions)
5. **`.claude/settings.json`** in the repo — project-level settings (committed)
6. **`.claude/settings.local.json`** in the repo — local project overrides (gitignored)

## Installing a template

1. Copy the desired template to your project root:
   ```bash
   cp claude/templates/CLAUDE.md.elixir-phoenix ~/repos/my-app/CLAUDE.md
   ```
2. Replace all `{{PLACEHOLDERS}}` with project-specific values.
3. Commit the file to the project repo.

## Layout

```
claude/
├── README.md                               # this file
└── templates/
    └── CLAUDE.md.elixir-phoenix            # Elixir/Phoenix project template
```

## Templates

| File | Purpose |
|------|---------|
| `CLAUDE.md.elixir-phoenix` | Full `CLAUDE.md` for an Elixir/Phoenix project: quality standards, architecture, git workflow, PR checklist |

## Notes

- `CLAUDE.md` is the primary way to give Claude Code project context and rules.
- Keep it concise — Claude Code reads it on every session start.
- Project-specific details (DB ports, context module names, API keys) belong in the project's own `CLAUDE.md`, not here.
- The PR submission checklist references OpenClaw skills (`pr-screenshots`, `pr-submit`) — install those separately if using OpenClaw.
