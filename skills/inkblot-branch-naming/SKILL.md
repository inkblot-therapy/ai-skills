---
name: inkblot-branch-naming
description: Name Inkblot git branches. Use this skill when you create a branch, start work on a Jira ticket, or name a pull request branch.
---

# Inkblot branch naming

Use this rule only when the work has a Jira ticket.
Skip this rule when the work has no ticket.
Do not invent a ticket prefix.
Do not ask for a ticket.

## Ticket work

Start the branch with the full Jira key in lowercase.
Do not add a type prefix such as `feat/`, `fix/`, or `feature/`.

```text
<ticket-id>-<short-kebab>
```

1. Use the full key in lowercase.
   Use one primary ticket.
   Write `pod2-2291`.
   Do not write `2-2291`.
2. Write the kebab in lowercase.
   Use 3 to 5 words.
   Name the change.
   Do not copy the ticket title.
3. Keep the full name short.
   Aim for about 50 characters.
   GitHub must show the name in the branch list.

### Examples

- `pod2-2291-video-call-no-show`
- `pod1-3452-company-renewal-dates`
- `pod1-3459-coaching-duration-options`

### Do not use these forms for ticket work

- Do not add a prefix before the ticket id.
  Examples: `fix/pod2-2291-...`, `feat/...`, `feature/...`, `bf-...`.
- Do not shorten the project key.
  Example: `2-2291-...`.
- Do not use uppercase.
  Example: `POD2-2291-...`.
- Do not put initials in the name.
  Example: `ji-pod1-2178-...`.
- Do not add the target environment.
  Examples: `-qa`, `-master`.
- Do not stack ticket ids.
  Example: `pod2-2276-2277-2282-...`.
  Use the primary ticket.
- Do not copy the full Jira title into the kebab.

## No ticket

Do not apply a naming rule.
Use any name.
Examples: `hotfix/7.54.2`, `sync/...`, `qa-kuma`.

## Why

- Put the ticket first.
  Do not add a type prefix.
  Jira and `git grep` need the key at the start.
  A type prefix only adds length.
- Use the full key in lowercase.
  `pod2-2291` is still the Jira key.
  Linking still works.
  Lowercase avoids case differences between macOS and Linux.
  `2-2291` is not a Jira key.
  GitHub and Jira will not attach that branch.
- Apply the rule only when a ticket exists.
  Do not block work that has no ticket.
  Do not rename work that has no ticket.
- Keep the kebab short.
  GitHub cuts a long branch name.
  The ticket title already lives in Jira.
