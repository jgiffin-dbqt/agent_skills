# Cursor — shared skills and templates

Generic agent skills and copy-paste templates for DBQ Technologies. **Project-specific** scripts, paths, and credentials stay in each application repo; copy and specialize what you need from here.

## Layout

```
cursor/
├── README.md                 # this file
├── templates/
│   ├── SKILL.md.template     # front matter + body skeleton for new skills
│   └── cursorrules.template.md
└── skills/
    ├── elixir-completion-checklist/
    ├── git-worktree-dev-environment/
    └── submit-pr-github-bugbot/
```

## Templates

| File | Purpose |
|------|---------|
| `templates/SKILL.md.template` | Canonical `name` / `description` YAML and section outline for Cursor skills |
| `templates/cursorrules.template.md` | Starter sections for a repo-root `.cursorrules` |

Replace `{{PLACEHOLDERS}}` when copying into a project. Keep skill `name` values unique, lowercase, hyphen-separated.

## Skills (generic)

| Skill | When to use |
|-------|-------------|
| `elixir-completion-checklist` | Before completing Elixir/Phoenix tasks: format, compile, test, dialyzer |
| `git-worktree-dev-environment` | Parallel agents/branches: worktrees, ports, DB isolation |
| `submit-pr-github-bugbot` | Open a GitHub PR, triage Bot + human review, iterate until green |

Each skill is self-contained Markdown with variables described in the body (e.g. `ELIXIR_PROJECT_ROOT`, `GITHUB_OWNER`, `GITHUB_REPO`). No symlinks are required: copy the folder you need into the target repo under `.cursor/skills/<name>/` and fill in values.

## Adopting in a repository

1. Copy one skill directory to `<your-repo>/.cursor/skills/<skill-name>/`.
2. Replace placeholders and add any commands your stack requires.
3. Optionally add or merge `templates/cursorrules.template.md` into `.cursorrules`.
4. Commit; Cursor discovers skills from the project workspace.
