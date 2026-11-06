# ai-skills

This repo shares [Agent Skills](https://agentskills.io) for Inkblot developers, product managers, and QA.
A skill teaches an AI tool to do a team task the same way each time.
The tools include Cursor, GitHub Copilot CLI, Claude Code, Codex, and any tool that follows the Agent Skills spec.

## Skills

| Skill | Purpose |
|---|---|
| [`inkblot-jira-ticket`](skills/inkblot-jira-ticket/SKILL.md) | Write a Jira ticket with a fixed structure. Put acceptance criteria where QA can find them. Use plain language above the divider. Put technical detail under Engineering Notes. |
| [`inkblot-branch-naming`](skills/inkblot-branch-naming/SKILL.md) | Name a git branch. Use `pod2-2291-short-kebab` when a ticket exists. Do not apply a rule when no ticket exists. |
| [`inkblot-commit-message`](skills/inkblot-commit-message/SKILL.md) | Write a git commit subject. Use `POD2-2291: Fix ...` when a ticket exists. Do not use Conventional Commits. Do not apply a rule when no ticket exists. |
| [`inkblot-simplified-technical-english`](skills/inkblot-simplified-technical-english/SKILL.md) | Write prose in Simplified Technical English (STE). Limit sentence length. Use active voice and simple words. Put one instruction in each sentence. |

## Optional: Simplified Technical English

This skill is not on `main`.
Install tag `ste-v1` when you want it.
The tag points at branch `add-simplified-technical-english-skill`.

```sh
npx skills add https://github.com/inkblot-therapy/ai-skills/tree/ste-v1/skills/inkblot-simplified-technical-english -g
```

Use the tree URL.
It checks out that tag.
Do not use `inkblot-therapy/ai-skills@ste-v1` alone.
Some CLI versions install `main` instead of the tag.

## Install

Use the [skills CLI](https://skills.sh).
It works for Cursor, Claude Code, Copilot CLI, Codex, and other tools.

```sh
# Pick the skills and the agents you want. Use this path.
npx skills add inkblot-therapy/ai-skills -g

# Install one skill.
npx skills add inkblot-therapy/ai-skills -g -s inkblot-jira-ticket

# List the skills. Do not install them.
npx skills add inkblot-therapy/ai-skills -l
```

Omit `-g` to install the skill for one project.

Update later with this command:

```sh
npx skills update
```

### Manual install

Use this path when Node is not available.
Clone the repo.
Link a skill folder into the skills directory for your tool.

```sh
git clone git@github.com:inkblot-therapy/ai-skills.git
ln -s "$PWD/ai-skills/skills/inkblot-jira-ticket" ~/.copilot/skills/   # Copilot CLI
ln -s "$PWD/ai-skills/skills/inkblot-jira-ticket" ~/.claude/skills/    # Claude Code
ln -s "$PWD/ai-skills/skills/inkblot-jira-ticket" ~/.cursor/skills/    # Cursor
```

Run `git pull` to update every linked tool.

## Contributing

1. Put one skill in each folder under `skills/<skill-name>/`.
   Add a `SKILL.md` file.
   Include `name` and `description` in the frontmatter.
   Follow the [Agent Skills spec](https://agentskills.io).
2. Keep each skill small.
   Write each instruction as you would brief a new teammate.
3. Open a pull request.
   Ask the pod that uses a convention to review a change to that convention.
   The Jira ticket format is one example.
