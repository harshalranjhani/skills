# skills

Personal agent skills for Cursor, Claude Code, Codex, OpenCode, and other agents that support the [Agent Skills](https://skills.sh) format.

Install any skill from this repo with the `skills` CLI (`npx skills`).

## Install

Global (all projects, all agents on this machine):

```bash
npx skills add harshalranjhani/skills -g --agent '*' -y
```

One skill only:

```bash
npx skills add harshalranjhani/skills --skill preview -g --agent '*' -y
```

Project-scoped (committed with the app):

```bash
npx skills add harshalranjhani/skills --skill preview -y
```

List what this repo ships:

```bash
npx skills add harshalranjhani/skills -l
```

Update after you pull new skills:

```bash
npx skills update -g
```

## Skills

| Skill | Description |
| --- | --- |
| [`preview`](skills/preview/SKILL.md) | Publish a temporary HTTPS demo URL with [preview-cli](https://github.com/harshalranjhani/preview-cli), share it, then stop it after review |

## Add a new skill

```bash
npx skills init skills/my-skill
```

Or create `skills/<name>/SKILL.md` with YAML frontmatter:

```markdown
---
name: my-skill
description: What it does and when agents should use it. Include trigger phrases.
---

# my-skill

Instructions for the agent…
```

Requirements:

- Folder name matches `name` in frontmatter (lowercase, hyphens)
- `description` covers **what** and **when** (third person)
- Keep `SKILL.md` concise; put deep reference in sibling files if needed

Commit, push to `main`, then reinstall or `npx skills update -g`.

## Local development

From this checkout:

```bash
npx skills add . --skill preview -g --agent '*' -y
```

## Publishing

There is no separate npm package to publish for skills.

1. Push this repo to GitHub (public if you want anyone to install it).
2. People install with `npx skills add harshalranjhani/skills`.
3. skills.sh discovers repos from install telemetry after the first installs.

`npx` only runs the [skills](https://www.npmjs.com/package/skills) CLI; the skill content is loaded from GitHub.
