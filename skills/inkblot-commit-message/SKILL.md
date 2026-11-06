---
name: inkblot-commit-message
description: Write Inkblot git commit subjects. Use this skill when you write a commit message, amend a subject, or format a commit.
---

# Inkblot commit messages

Use this rule only when the work has a Jira ticket.
Skip this rule when the work has no ticket.
Do not invent a ticket id.
Do not ask for a ticket.
Do not change the subject to fit this rule.

## Ticket work

```text
<TICKET-ID>: <Concise imperative summary>
```

1. Use the full Jira key in uppercase.
   Use one ticket for each commit.
   Write `POD2-2291`.
   Do not write `pod2-2291`.
   Do not write `2-2291`.
2. Put a colon and one space after the key.
   Write an imperative summary.
   Start with a verb such as Fix, Add, or Rename.
3. Use sentence case.
   Use one line.
   Aim for about 72 characters.
   State the outcome.
   Do not list files.
4. Put the ticket for this commit in the subject.
   Do not list every ticket on the pull request.
   Do not always copy the branch name.

Do not use a Conventional Commits prefix.
Examples: `feat:`, `fix:`, `chore:`.
The ticket classifies the work.

### Several tickets on one piece of work

A branch or a pull request may list several tickets.
Give each commit one key.
Use the ticket that the commit implements or fixes.

- Use the branch ticket when the commit is for that work.
  A branch named `pod2-2291-...` uses the subject `POD2-2291: ...`.
- Switch the subject key when this commit is for a different linked ticket.
- Ask when the ticket for this commit is unclear.
  Do not invent an id.
  Do not stack keys.
  Do not write `POD2-2276-2277: ...`.

Example. The branch `pod2-2276-restrict-provider-access` is also linked to `POD2-2277`.

- `POD2-2276: Restrict availability reserved-for-client`
- `POD2-2277: Hide estimates from unauthorized clients`

### Examples

- `POD2-2291: Fix no-show video call outcome`
- `POD2-2291: Baseline the false-positive Brakeman role warning`
- `POD1-3452: Rename renewal date service to resolver`
- `POD1-3452: Guard missing specialized renewal periods`

### Body

Do not require a body.
Do not add a body unless the user asks.
Keep extra context in Jira.

### Do not use these forms for ticket work

- Do not write `feat: add renewal dates` or `fix: ...`.
  Do not use Conventional Commits.
- Do not write `pod2-2291: Fix no-show ...`.
  Use uppercase for the key in a commit.
- Do not write `2-2291: Fix no-show ...`.
  That text is not a Jira key.
- Do not write `POD2-2291 - fix stuff`.
  Use `: `.
  Do not use ` - `.
- Do not write `POD2-2291: Fixed bug`.
  Use the imperative form `Fix ...`.
- Do not write `POD2-2276-2277: ...`.
  Put one primary ticket in the subject.
- Do not invent a ticket id.

### Validation for a hook or a linter

```text
^[A-Z][A-Z0-9]+-[0-9]+: .+
```

## No ticket

Do not apply a subject rule.
Use any subject for a merge commit, a sync, or other work with no ticket.

## Why

- Put the ticket in the subject.
  Jira Smart Commits read the commit subject.
  GitHub to Jira linking reads the commit subject.
  The branch name is not enough.
- Use uppercase in a commit subject.
  Use lowercase in a branch name.
  Trackers expect the form `POD2-2291:`.
  A lowercase branch avoids a git case clash.
- Do not add `feat:` or `fix:`.
  The ticket already classifies the work.
  A second prefix only makes the subject longer.
- Apply the rule only when a ticket exists.
  Use the same limit as branch names.
  Do not block a chore, a merge, or a hotfix that has no ticket.
- Use one ticket for each commit.
  Do not use one ticket for the whole branch.
  Jira attaches the commit to the key in the subject.
  When one pull request covers several tickets, point each commit at the ticket it changes.
  A stacked id breaks search, hooks, and that attachment.
- Do not set a body rule.
  The ticket already has the context.
  A second paragraph in git does not help Jira linking.
  An agent can invent that paragraph.
