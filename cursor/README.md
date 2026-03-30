# Cursor — shared skills, rules, and templates

Generic agent skills, **reusable `.cursorrules` fragments**, and copy-paste templates for DBQ Technologies. **Project-specific** scripts, paths, and credentials stay in each application repo; copy and specialize what you need from here.

## How Cursor applies rules

- Cursor uses **`.cursorrules` at the root of the repo (or workspace folder) you are working in**. That file in the **target project** is the **canonical** source for agent behavior in that tree.
- This **`agent_skills`** repository is **not** applied automatically to other repos unless you copy merged content into them or open **`agent_skills`** itself as a workspace root with its own rules file. Treat **`agent_skills` as upstream**: fragments and templates live here; each product repository **merges** them into its local `.cursorrules` with real names, paths, and stack specifics.

## Practical workflow

1. **Day to day** — Edit **`<your-project>/.cursorrules`** in the repository where the work happens.
2. **When generic policy changes** — Update fragments under `cursor/rules/` or templates under `cursor/templates/` **in this repo** first, then merge the same ideas into each downstream `.cursorrules` so shared wording stays aligned.
3. **Optional multi-root workspace** — Add a clone of **`agent_skills`** as another folder in Cursor for **reference and copy-paste** while editing a project’s `.cursorrules`. The agent still follows each project’s **`.cursorrules`** for that folder; the extra root does not replace per-repo rules unless you add rules there intentionally.
4. **Symlinks** — Optional; **copy/merge** is the default workflow.

## Layout

```
cursor/
├── README.md                 # this file
├── rules/                    # generic Cursor rule fragments (merge or copy)
│   ├── README.md
│   ├── implementation-plans.md
│   ├── architecture-versioning.md
│   ├── references-in-plans.md
│   ├── best-practices.md
│   └── planning-checklists.md
├── templates/
│   ├── SKILL.md.template
│   ├── cursorrules.template.md              # minimal empty shell
│   └── cursorrules.planning-repository.md   # full planning + implementation doc split
└── skills/
    ├── elixir-completion-checklist/
    ├── git-worktree-dev-environment/
    └── submit-pr-github-self-review/
```

## Rules (fragments)

Modular sections for documentation/planning workflows. See [`rules/README.md`](rules/README.md) for the index. Use them to **patch** an existing `.cursorrules` or to build a new one section by section.

| Fragment | Topics |
|----------|--------|
| `implementation-plans.md` | Plan location, naming, template, GitHub issue workflow |
| `architecture-versioning.md` | Version folders for architecture docs |
| `references-in-plans.md` | Code and cross-document references in plans |
| `best-practices.md` | Writing actionable plans |
| `planning-checklists.md` | Multi-tenancy, security, performance prompts |

## Templates

| File | Purpose |
|------|---------|
| `templates/SKILL.md.template` | Canonical `name` / `description` YAML and section outline for Cursor skills |
| `templates/cursorrules.template.md` | Minimal starter: overview, conventions, boundaries, security, references |
| `templates/cursorrules.planning-repository.md` | **All-in-one** generic rules for a planning repo + implementation repo split (replace `{{PLACEHOLDERS}}`) |

Replace `{{PLACEHOLDERS}}` when copying into a project. Keep skill `name` values unique, lowercase, hyphen-separated.

## Skills (generic)

| Skill | When to use |
|-------|-------------|
| `elixir-completion-checklist` | Before completing Elixir/Phoenix tasks: format, compile, test, dialyzer |
| `git-worktree-dev-environment` | Parallel agents/branches: worktrees, ports, DB isolation |
| `submit-pr-github-self-review` | Open a GitHub PR, cold subagent self-review (PR URL/number only), fix and repeat until clean |

Each skill is self-contained Markdown with variables described in the body (e.g. `ELIXIR_PROJECT_ROOT`, `GITHUB_OWNER`, `GITHUB_REPO`). No symlinks are required: copy the folder you need into the target repo under `.cursor/skills/<name>/` and fill in values.

## Adopting in a repository

1. **Rules:** Merge `cursor/rules/*.md` sections into the project’s `.cursorrules`, or start from `templates/cursorrules.planning-repository.md` and replace placeholders. See **How Cursor applies rules** and **Practical workflow** above.
2. **Skills:** Copy a skill directory to `<your-repo>/.cursor/skills/<skill-name>/`, edit `SKILL.md`, commit.
3. Cursor discovers skills from the project workspace.
