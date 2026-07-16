---
name: inkblot-jira-ticket
description: Inkblot Jira ticket writing convention. Use whenever creating, drafting, or editing a Jira ticket on inkblottherapy.atlassian.net (POD1, POD2, or any pod) — Tasks, Stories, Bugs, Spikes, and Sub-tasks. Ensures consistent structure and discoverable acceptance criteria for QA.
---

# Inkblot Jira Ticket Convention

When creating or editing a ticket, structure the description exactly as below.
Fixed heading names — do not rename, reorder, or invent sections.

## Two hard rules

1. Acceptance criteria MUST live in the description body under a `## Acceptance criteria`
   heading, as a checklist. NEVER put them only in Jira's side-panel "Acceptance criteria"
   checklist field — QA, exports, PR links, and AI tools miss it there.
2. `## Context`, `## Scope`, `## Acceptance criteria`, and `## How to test` MUST use plain,
   human-friendly language that non-technical readers (QA, PM, support) can understand.
   Anything that only makes sense to developers — code paths, class/function names, SQL,
   stack traces, architecture detail — belongs under `## Engineering Notes`, below the
   `---` divider.

## Template: Task / Story

```markdown
## Context
Why this exists, 2–4 lines, in plain language. If there is a real end user, open with:
"As a <user>, I want <goal> so that <value>." Otherwise plain prose — never force a user story.

## Scope
What's in. What's explicitly out. Described as user/system behavior, not implementation.

## Acceptance criteria
* Testable, QA-facing statements written as observable behavior
* Include env / feature-flag prerequisites QA needs

## How to test
(Optional — include ONLY when verification setup isn't obvious. Plain language:
URLs/routes to visit, test accounts to use, flags to toggle, data setup steps.
Skip entirely if the acceptance criteria are self-explanatory.)

---

## Engineering Notes
(Optional, ALWAYS last, after the --- divider.)
Root cause analysis, code paths, PR/branch links, implementation ideas,
migration/rollback steps, log/NewRelic links. Nothing QA needs may live here.
```

## Template: Bug

```markdown
## Steps to reproduce
1. ...

## Expected
What should happen.

## Actual
What happens instead (screenshots / errors).

## Environment
* Env:
* Browser / client:
* Account / feature flags:

## Acceptance criteria
* The observable behavior that proves the fix

## How to test
(Optional — only if retesting needs non-obvious setup beyond the repro steps.)

---

## Engineering Notes
(Optional, last — root cause, suspect code, links.)
```

## Template: Spike

```markdown
## Question
What we need to answer.

## Timebox
e.g. 2 days.

## Deliverable
Doc / decision / prototype / follow-up tickets.
```

## Rules

- QA reads top-down and stops at the `---` divider. Everything above it must be
  understandable without reading code or knowing the codebase.
- Engineering Notes is the pressure valve: put ALL technical detail there, not
  interleaved through the ticket. When in doubt whether something is "too technical",
  move it to Engineering Notes.
- Summary (title): concise, imperative, specific. No trailing period.
- Do not backfill old tickets to this format.

## Jira specifics (inkblottherapy.atlassian.net)

- Projects are company-managed / classic (POD2 project id 10013).
- POD2 Story Points field is `customfield_10033` (NOT customfield_10016). Verify the
  Story Points field per project via createmeta before setting points elsewhere.
- API/MCP limitation: the markdown path cannot create native Jira checkboxes —
  `- [ ]` renders as escaped literal text. Use plain `*` bullets under
  `## Acceptance criteria`.

## Self-check before submitting

1. Does `## Acceptance criteria` exist in the description body with ≥1 testable item?
2. Could a non-technical reader (QA, PM, support) understand everything above the `---`,
   including `## How to test` if present?
3. Is all technical detail (code paths, PRs, root cause, dev jargon) below the `---`
   in `## Engineering Notes`?
4. Task/Story: is a user-story opening line used ONLY if a genuine end user exists?
