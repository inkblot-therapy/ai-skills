---
name: inkblot-jira-ticket
description: Inkblot Jira ticket writing convention. Use whenever creating, drafting, or editing a Jira ticket on inkblottherapy.atlassian.net (POD1, POD2, or any pod) — Tasks, Stories, Bugs, Spikes, and Sub-tasks. Ensures consistent structure and discoverable acceptance criteria for QA.
---

# Inkblot Jira Ticket Convention

When creating or editing a ticket, structure the description exactly as below.
Fixed heading names — do not rename, reorder, or invent sections.

## Three hard rules

1. NO PII or PHI in tickets — ever. No patient/client names, emails, phone numbers,
   health card numbers, diagnoses, session content, or anything identifying a person.
   Refer to people by role + opaque ID (e.g. "client user_id 12345", "appointment
   977096", support ref "ATS-13371"). Redact identifying details from screenshots,
   logs, and stack traces before attaching. This applies to every section, including
   Engineering Notes and comments.
2. Acceptance criteria MUST live in the description body under a `## Acceptance criteria`
   heading, as a checklist. NEVER put them only in Jira's side-panel "Acceptance criteria"
   checklist field — QA, exports, PR links, and AI tools miss it there.
3. `## Context`, `## Scope`, `## Acceptance criteria`, and `## How to test` (when present)
   MUST use plain, human-friendly language that non-technical readers (QA, PM, support)
   can understand. Anything that only makes sense to developers — code paths,
   class/function names, SQL, stack traces, architecture detail — belongs under
   `## Engineering Notes`, below the `---` divider.

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
(Optional — include ONLY when the user explicitly asks for it. Plain language:
URLs/routes to visit, test accounts to use, flags to toggle, data setup steps.
Omit by default.)

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
(Optional — include ONLY when the user explicitly asks for it.)

---

## Engineering Notes
(Optional, last — root cause, suspect code, links.)
```

### Bugs a human cannot reproduce (infra / outages / race conditions)

If the repro is not something a person can perform on demand (an upstream outage,
a server restart, a timing race, corrupted state), do NOT write pseudo-repro steps.
Replace `## Steps to reproduce` with `## What happened` — a short plain-language
narrative of the incident and its user-visible impact ("During the X outage on
<date>, some users experienced …"). Keep `## Expected` / `## Actual` focused on
symptoms. `## How to test` lists only checks QA can actually perform; the technical
trigger conditions and any dev-only verification (logs, Redis, metrics) go under
`## Engineering Notes`.

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
- Checkboxes: to get real, tickable Jira checkboxes under `## Acceptance criteria`,
  create/edit the description as ADF (`contentFormat: "adf"`) using `taskList` /
  `taskItem` nodes — this reliably produces interactive checkboxes (verified by
  write-then-read-back). The markdown path is unreliable for `- [ ]` (sometimes a
  real task list, sometimes literal `[ ]` text) — only if you must use markdown,
  fall back to plain `*` bullets.

## Self-check before submitting

1. Is the ticket free of PII/PHI (names, emails, phone numbers, health data,
   session content) — including screenshots, logs, and Engineering Notes?
2. Does `## Acceptance criteria` exist in the description body with ≥1 testable item?
3. Could a non-technical reader (QA, PM, support) understand everything above the `---`,
   including `## How to test` if present?
4. Is `## How to test` omitted unless the user explicitly asked for it?
5. Is all technical detail (code paths, PRs, root cause, dev jargon) below the `---`
   in `## Engineering Notes`?
6. Task/Story: is a user-story opening line used ONLY if a genuine end user exists?
