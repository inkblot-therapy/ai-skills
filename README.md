# ai-skills

Shared [Agent Skills](https://agentskills.io) for Inkblot devs, PMs, and QA.
Skills teach AI tools (Cursor, GitHub Copilot CLI, Claude Code, Codex, and any
Agent Skills–compliant tool) to perform team tasks in a consistent, repeatable way.

## Skills

| Skill | Purpose |
|---|---|
| [`inkblot-jira-ticket`](skills/inkblot-jira-ticket/SKILL.md) | Jira ticket writing convention — consistent structure, QA-discoverable acceptance criteria, plain language above the fold, technical detail under Engineering Notes. |

## Install

With the [skills CLI](https://skills.sh) (works for Cursor, Claude Code,
Copilot CLI, Codex, and more):

```sh
# interactive — pick the skills and agents you want (recommended)
npx skills add inkblot-therapy/ai-skills -g

# install a specific skill directly
npx skills add inkblot-therapy/ai-skills -g -s inkblot-jira-ticket

# see what's available without installing
npx skills add inkblot-therapy/ai-skills -l
```

Drop `-g` to install project-level instead of globally.

To update later:

```sh
npx skills update
```

### Manual install (no Node)

Clone and symlink a skill folder into your tool's skills directory, e.g.:

```sh
git clone git@github.com:inkblot-therapy/ai-skills.git
ln -s "$PWD/ai-skills/skills/inkblot-jira-ticket" ~/.copilot/skills/   # Copilot CLI
ln -s "$PWD/ai-skills/skills/inkblot-jira-ticket" ~/.claude/skills/    # Claude Code
ln -s "$PWD/ai-skills/skills/inkblot-jira-ticket" ~/.cursor/skills/    # Cursor
```

`git pull` then updates all tools at once.

## Contributing

1. One folder per skill under `skills/<skill-name>/` containing a `SKILL.md`
   with `name` and `description` frontmatter ([spec](https://agentskills.io)).
2. Keep skills small and focused; write instructions the way you'd brief a new teammate.
3. Open a PR — changes to a convention (like the Jira ticket format) should get
   a quick look from the pod that uses it.
