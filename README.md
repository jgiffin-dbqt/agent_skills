# agent_skills

AI agent skills and configuration for DBQ Technologies that define development processes and best practices used across all projects.

Any project-specific skills belong in the project repo.

## Layout

```
├── claude/                    # Claude Code config and templates
│   ├── README.md
│   └── templates/
│       └── CLAUDE.md.elixir-phoenix
├── cursor/                    # Cursor rules, skills, and templates
│   ├── README.md
│   ├── rules/                 # generic .cursorrules fragments
│   ├── skills/                # reusable Cursor skills
│   └── templates/             # starter templates
└── openclaw/                  # OpenClaw skills
    ├── README.md
    └── skills/
        ├── elixir-completion-checklist/   # format, compile, test, dialyzer gate
        ├── git-worktree-dev-environment/  # isolated parallel development
        ├── pr-screenshots/                # capture UI screenshots for GitHub PRs
        └── pr-submit/                     # PR submission with cold self-review loop
```

## Agents

| Directory | Agent | Config model |
|-----------|-------|--------------|
| `claude/` | [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | `CLAUDE.md` at repo root + `~/.claude/settings*.json` |
| `cursor/` | [Cursor](https://cursor.sh) | `.cursorrules` at repo root + `.cursor/skills/` |
| `openclaw/` | [OpenClaw](https://docs.openclaw.ai) | `~/.openclaw/workspace/skills/` |

See each directory's `README.md` for installation and usage details.

## Philosophy

- **Generic over specific** — skills here work across projects. Project-specific paths, credentials, and context live in each project repo.
- **Copy, don't symlink** — copy skills into your project or agent workspace, then adapt. Symlinks are fragile across updates and machines.
- **Version control everything** — agent config is code. Changes get commits, reviews, and history.
