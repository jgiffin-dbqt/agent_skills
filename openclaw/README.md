# OpenClaw — shared skills

Generic agent skills for [OpenClaw](https://docs.openclaw.ai). Project-specific paths, credentials, and workflow details stay in each project's workspace; copy and adapt what you need from here.

## How OpenClaw discovers skills

OpenClaw loads skills from two locations:

1. **Built-in:** `<openclaw-install>/skills/` (shipped with the package, overwritten on update)
2. **Workspace:** `~/.openclaw/workspace/skills/` (user-managed, survives updates)

Workspace skills take precedence when names collide with built-in skills.

## Installing a skill from this repo

Copy the skill directory into your OpenClaw workspace:

```bash
cp -r openclaw/skills/<skill-name> ~/.openclaw/workspace/skills/<skill-name>
```

OpenClaw picks it up immediately — no restart needed.

## Updating after an OpenClaw upgrade

OpenClaw updates can overwrite built-in skills but **never touch workspace skills**. That's why custom skills belong in `~/.openclaw/workspace/skills/`, not in the install directory.

If an update removes or changes a built-in skill you depend on, the workspace copy still works.

## Layout

```
openclaw/
├── README.md                          # this file
└── skills/
    ├── pr-screenshots/SKILL.md        # capture UI screenshots for GitHub PRs
    └── pr-submit/SKILL.md             # PR submission with cold self-review loop
```

## Skill format

OpenClaw skills use a `SKILL.md` file with YAML frontmatter:

```yaml
---
name: skill-name
description: "When to use this skill."
metadata:
  {
    "openclaw": {
      "emoji": "🔧",
      "requires": { "anyBins": ["some-cli"] }
    }
  }
---
```

The body is Markdown with instructions the agent follows. See [OpenClaw docs](https://docs.openclaw.ai) for the full spec.
