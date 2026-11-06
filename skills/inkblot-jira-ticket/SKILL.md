---
name: inkblot-jira-ticket
description: Write Inkblot Jira tickets. Use this skill to create, draft, edit, or review a ticket on inkblottherapy.atlassian.net for any pod. Cover Tasks, Stories, Bugs, Spikes, and Sub-tasks.
---

# Inkblot Jira ticket convention

Use this structure when you create or edit a ticket.
Keep each heading name fixed.
Do not rename a section.
Do not reorder a section.
Do not add a section.

## Three hard rules

1. Do not put PII or PHI in a ticket.
   Do not include a person name.
   Do not include an email.
   Do not include a phone number.
   Do not include a health card number.
   Do not include a diagnosis.
   Do not include session content.
   Do not include any detail that identifies a person.
   Name a person by role and an opaque id.
   Example: "client user_id 12345".
   Example: "appointment 977096".
   Example: support ref "ATS-13371".
   Remove identifying details from a screenshot before you attach it.
   Remove identifying details from a log before you attach it.
   Remove identifying details from a stack trace before you attach it.
   Apply this rule to every section.
   Apply this rule to Engineering Notes.
   Apply this rule to comments.
2. Put acceptance criteria in the description body.
   Use the heading `## Acceptance criteria`.
   Write the criteria as a checklist.
   Write the description as ADF.
   Set `contentFormat` to `"adf"`.
   Render the criteria as a real tickable `taskList`.
   Read "Jira specifics" for the steps.
   Do not put the criteria only in the side-panel Acceptance criteria field.
   QA misses that field.
   An export misses that field.
   A pull request link misses that field.
   An AI tool misses that field.
3. Use plain language in these sections: `## Context`, `## Scope`, `## Acceptance criteria`, and `## How to test`.
   A reader in QA, product, or support must understand those sections.
   Put developer-only detail under `## Engineering Notes`.
   Put that section below the `---` divider.
   Developer-only detail includes a code path, a class name, a function name, SQL, a stack trace, and an architecture note.

## Template: Task or Story

```markdown
## Context
Write 2 to 4 lines.
Use plain language.
State why this work exists.
Open with a user story only when a real end user exists.
Use this line: "As a <user>, I want <goal> so that <value>."
Do not force a user story.

## Scope
State what is in scope.
State what is out of scope.
Describe user behavior or system behavior.
Do not describe the implementation.

## Acceptance criteria
* Write each item as observable behavior.
* Write each item so QA can test it.
* Include the environment or feature-flag setup that QA needs.

## How to test
Include this section only when the user asks for it.
Follow "How to test writing guidance" when you include it.
Name the exact app and the URL.
Name each Network step in the browser developer tools.
Name the exact request and the host.
Name the response field and the expected value.
Omit this section by default.

---

## Engineering Notes
Keep this section optional.
Put this section last, after the --- divider.
Include the root cause.
Include code paths.
Include pull request links and branch links.
Include an implementation idea.
Include migration steps and rollback steps.
Include a log link or a New Relic link.
Do not put a QA need in this section.
```

## Template: Bug

```markdown
## Steps to reproduce
1. ...

## Expected
State what should happen.

## Actual
State what happens instead.
Add a screenshot or an error when you have one.

## Environment
* Env:
* Browser / client:
* Account / feature flags:

## Acceptance criteria
* State the observable behavior that proves the fix.

## How to test
Include this section only when the user asks for it.
Follow "How to test writing guidance" when you include it.
Name the exact app and the URL.
Name each Network step.
Name the exact request and the host.
Name the response field and the expected value.

---

## Engineering Notes
Keep this section optional.
Put this section last.
Include the root cause, the suspect code, and links.
```

### A person cannot reproduce the bug

Use this form for an upstream outage, a server restart, a timing race, or corrupt state.
Do not write fake steps to reproduce.
Replace `## Steps to reproduce` with `## What happened`.
Write a short plain account of the incident.
State the impact that a user can see.
Example: "During the X outage on <date>, some users saw ..."
Keep `## Expected` and `## Actual` on the symptoms.
In `## How to test`, list only a check that QA can perform.
Put the technical trigger under `## Engineering Notes`.
Put a developer-only check under `## Engineering Notes`.
A developer-only check includes a log, Redis, or a metric.

## Template: Spike

```markdown
## Question
State the question that the spike must answer.

## Timebox
State the time limit.
Example: 2 days.

## Deliverable
State the output.
Use a doc, a decision, a prototype, or follow-up tickets.
```

## How to test writing guidance

`## How to test` is optional.
Include it only when the user asks for it.
When you include it, write steps that a QA tester can follow without the code.
Write a numbered list.
Be specific.
Do not write only "open the web app."

Use this pattern for behavior in the UI or in an API response.

1. Name the exact app.
   Name the URL.
   Example: the Practice app at `https://practice.inkblottherapy.com`.
   You may name the client web app, or the mobile app and the build.
   State the environment, such as staging or production.
   State the test account or the role when that choice matters.
2. State the setup.
   Name the data by an opaque id.
   Name each feature flag to change.
   State each precondition.
3. Open the browser developer tools when the check is in a network response.
   Right-click the page.
   Choose Inspect, or press F12.
   Open the Network tab.
   Reload the page.
4. Name the exact request.
   State the method and the full path.
   State the host.
   Example: `https://api.inkblottherapy.com/api/appointments/{id}`.
   State the filter keyword that finds the request.
   Open the Response tab or the Preview tab.
5. Name each field to check.
   State the expected value for each state.
   Example: `video_provider` is `vonage` when the flag is on.
   Example: `video_provider` is `twilio` when the flag is off.
6. Give the check loop.
   Change the flag or the input.
   Reload the page.
   Confirm that the value changes as expected.
   Add a second independent signal when you can.
   Example: a related endpoint returns 200, or it returns 404.

Keep each step above the `---` in language that QA understands.
Put a developer-only check in `## Engineering Notes`.
A developer-only check includes a log, Redis, a metric, or a database query.

## Authoring rules

QA reads from the top and stops at the `---` divider.
A reader must understand everything above the divider without the code.
Put all technical detail in Engineering Notes.
Do not mix technical detail through the ticket.
Move a detail to Engineering Notes when you are not sure it is too technical.
Write the summary in the imperative.
Keep the summary short and specific.
Do not end the summary with a period.
Do not rewrite old closed tickets in bulk.
Rewrite an open ticket that has not started when the assignee asks.

## Jira specifics for inkblottherapy.atlassian.net

Projects are company-managed classic projects.
The POD2 project id is 10013.
The POD2 Story Points field is `customfield_10033`.
Do not use `customfield_10016` for POD2 Story Points.
Check the Story Points field for each project in createmeta before you set points on another project.

Write the description as ADF when you create or edit checkboxes.
Set `contentFormat` to `"adf"`.
Render `## Acceptance criteria` as a `taskList` of `taskItem` nodes.
Set `attrs.localId` on each `taskItem`.
Set `state` to `"TODO"` on each `taskItem`.
This form makes a real tickable Jira checkbox.
Confirm the save by a write and then a read.
Do not rely on the markdown form `- [ ]`.
That path is not stable.
It sometimes saves a real task list.
It sometimes saves the literal text `[ ]`.
Use plain `*` bullets only when ADF is not available.

You may render `## Engineering Notes` as an ADF `expand` node.
Use that form when you want the technical detail closed by default.
Place the node below the `---` divider.
Use this shape:

```json
{ "type": "expand", "attrs": { "title": "Engineering Notes" }, "content": [] }
```

Put the block content in `content`.
Let the expand `title` replace the heading.
Do not emit both a `## Engineering Notes` heading and an expand with the same title.
Set `contentFormat` to `"adf"`.
The markdown path drops the `expand` wrapper.
The markdown path then shows the inner content in line.
The collapse is visual only in the Jira web UI.
The API always returns the full content.
An ADF fetch returns the content as saved.
A markdown fetch shows the content in line.
An AI agent that reads the ticket still sees the notes.
Confirm the save with a new fetch.
Set `responseContentFormat` to `"adf"` on that fetch.
The tool echo of the description is markdown.
That echo flattens `expand`.
The markdown echo alone cannot confirm that the node saved.

## Self-check before you submit

1. Confirm that the ticket has no PII or PHI.
   Check names, emails, phone numbers, health data, and session content.
   Check screenshots, logs, and Engineering Notes.
2. Confirm that `## Acceptance criteria` is in the description body.
   Confirm that it is an ADF `taskList` with real tickable checkboxes.
   Use plain `*` bullets only when ADF is not available.
   Include at least one item that QA can test.
3. Confirm that a non-technical reader can understand everything above the `---`.
   Include QA, product, and support as those readers.
   Include `## How to test` when that section is present.
4. When `## How to test` is present, confirm each step.
   Each step must name the exact app and the URL.
   Each step must name the exact request and the host.
   Each step must name the response field and the expected value.
   Follow "How to test writing guidance".
   Omit the section unless the user asked for it.
5. Confirm that all technical detail is below the `---`.
   Put it in `## Engineering Notes`.
   Technical detail includes a code path, a pull request, a root cause, and developer jargon.
6. For a Task or a Story, use a user-story opening line only when a real end user exists.

## Review pass

Run the Self-check as a graded review before you create the ticket.
Do not treat the check as a silent yes or no.
The review catches mistakes that a draft can hide.

Use two modes.

- Always run a self-review.
  After you draft the ticket, grade each Self-check item.
- Run an independent review for any ticket that is not trivial.
  Ask a separate agent or a fresh context to run the same rubric against the draft.
  The author of the ticket tends to approve their own draft.
  A fresh pass finds the real defects.
  Give the reviewer the rules in this skill.
  Give the reviewer the draft.
  Ask for the output format below.

Use this review output.

1. Write a compliance checklist.
   Grade each of the 6 Self-check items as PASS, FAIL, or N/A.
   Add one line of justification for each grade.
2. List each issue.
   Tag the severity.
   Add a concrete rewrite when it helps.
   - Use **blocker** when the draft breaks a hard rule or states a wrong fact.
     A hard-rule break includes PII or PHI in any section.
     A hard-rule break includes acceptance criteria that are not a `taskList` in the body.
     A hard-rule break includes developer-only detail above the `---`.
     Fix a blocker before you create the ticket.
   - Use **should-fix** when a gap will mislead QA or will misstate the scope.
     Example: the summary promises one behavior, and the acceptance criteria test another behavior.
     Fix a should-fix item unless you have a clear reason to keep it.
   - Use **nice-to-have** for wording polish.
     A nice-to-have item is optional.
3. Write a verdict.
   Use "ready to create as-is" when no change is required.
   Otherwise list the smallest changes to make first.

Check these misses on every review.
Each one has appeared on a real ticket.

- Check for hidden PII.
  A person name can sit in a branch name, a pull request link, a commit link, or a pasted log.
  Hard rule 1 applies to every section, including Engineering Notes.
- Check the title against the acceptance criteria.
  The summary must describe the same behavior that the criteria verify.
  Make the summary and the criteria agree.
- Check that acceptance criteria state observable behavior.
  Do not restate the implementation.
  Do not write "adds a CI job that runs RuboCop".
  Write "the check fails when ...".
- Check `## How to test`.
  Remove the section when nobody asked for it.
  Rewrite the section when it is too vague for a QA tester who has not seen the code.
