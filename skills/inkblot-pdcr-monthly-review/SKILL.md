---
name: inkblot-pdcr-monthly-review
description: Run the monthly PDCR review of direct production database changes for SOC 2. Use this skill when you run the monthly DB review, the PDCR monthly cycle, or the monthly pgAudit review.
---

# PDCR monthly database-change review

This review is a SOC 2 detective control (Deloitte 2026, control ticket POD2-2261).
Each month, it reconciles each direct production DB change with its authorizing PDCR ticket.
pgAudit records the changes.
Run the review by the 5th business day of the month.
Review the prior calendar month.

## Sources of truth

- The runbook is Confluence page `304480263` in space POD2.
  Read it first with `contentFormat: html`.
  The runbook wins when this skill and the runbook disagree.
- The report template is in runbook Step 4.
- The Evidence Log is runbook §6.
- A good example output is page `441614340`, "Monthly Review — Oct 2026".

## Prerequisites

- Log in with `az login`.
  Select subscription `prod-113`.
  The KQL queries do not need the VPN.
- Get an Atlassian API token for Confluence REST calls.
- Connect the Jira/Confluence MCP and the Slack MCP.
- Connect to the corporate VPN for SSH to the prod VMs.
- Get the current authorized-operator list from the control owner.
- Get the current approver list from the control owner.
  Do not hard-code either list.

## Scope

- In scope: pgAudit `WRITE`, `DDL`, and `ROLE` entries from operator sessions.
  An operator session is a direct change that a person ran.
  Review both regions.
- Out of scope: reads, app traffic (`puma`, `sidekiq`, `rake`), and Azure or Postgres housekeeping.
- Out of scope: before and after row values (POD2-2234).
  pgAudit shows parameters as `<not logged>`.

| Region | Server | Log Analytics workspace | Workspace ID |
|---|---|---|---|
| Canada | `prod438inkblotdb` | `pgaudit-prod438-ca-law` | `7300e076-81e7-4f77-aea9-a163467a4e1d` |
| USA | `prod439inkblotdb` | `pgaudit-prod439-us-law` | `5d656978-0cac-4b06-9d80-7fa511c7a623` |

Audit logs stay for 456 days.
VM `auth.log` files stay for about 4 weeks.
Default Entra PIM logs stay for about 30 days.
Do the VM and PIM steps early in the cycle.

## Stamp formats

`bin/pdcr` sets the Postgres `application_name` to a stamp.
A month can contain both formats.

| Format | Live from | Shape | Identity |
|---|---|---|---|
| v1 (POD2-2233) | Sep 2026 | `PDCR:<upn>:<ticket>` | Self-asserted |
| v2 (POD2-2337) | First prod release after 2026-10-27 | `PDCR:<ticket>:v:<first.last>` | Verified Entra login |
| v2 | First prod release after 2026-10-27 | `PDCR:<ticket>:s:<first.last>` | Self-asserted |

- `v` means verified.
  `pdcr-enter` on prod-846 (CA) or prod-813 (US) signed the operator's Entra `az ssh` identity.
- `s` means self-asserted.
  The operator typed the name.
  After the POD2-2337 release, an `s` stamp is an exception without a documented reason, such as break-glass.
- `bin/rails` means a plain Rails console or runner.
  The `pg` gem sets `application_name` to `$0`.
  `bin/pdcr` always shows `bin/pdcr` or a `PDCR:` stamp.
  A `bin/rails` session did not use `bin/pdcr`, even if the operator says so.

## Step 1 — Extract the population

Run each query in both workspaces.
Use the queries in [references/queries.md](references/queries.md).

1. Run the population query.
   It keeps all operator sessions and removes app and Azure traffic.
2. Run the stamp query.
   It parses the operator and ticket from each `PDCR:` stamp.
3. Treat each unstamped session as an exception.
   An unstamped session is usually `bin/rails`.
4. Run the read-write console check.
   A read-write console on the prod swarm without a PDCR stamp is a finding, even with no writes.
   Reads must use the debug swarm (`prod-1441-vm_debug`, credential `inkblot_ro`).
5. Export the PIM activations for the month.
   Write `pdcr-<YYYY-MM>-pim-activations.csv`.
   PIM gating on these resource groups started on 2026-10-27 (POD2-2337).

Parse the pgAudit statement field with a CSV reader.
The statement is CSV-quoted and can contain commas.

## Step 2 — Sample

- Review 100% when the population is 20 statements or fewer.
- Use a risk-based sample for a larger population.
  Example: 25%, with a minimum of 10.
- Review 100% of `DDL` and `ROLE` entries at all sizes.

## Step 3 — Attribute each session

- v2 `v` stamp: use the stamped identity.
  No more attribution is necessary.
- v1 or v2 `s` stamp: confirm the identity with the operator.
- Unstamped session: follow [references/attribution.md](references/attribution.md).
- Compare each operator with the authorized-operator list.
  A change by a person not on the list is an exception.

Do not record an actor without the operator's confirmation.

## Step 4 — Ask the operators

1. Open one question thread in Slack `#pdcr-team`.
2. Write numbered items for each operator.
3. Link the thread on the cycle page.

`#production-data-change-request` is a Slack Connect channel.
The Slack MCP can only create drafts there.
The tool cannot edit or delete a draft.
Tell the user which old drafts to delete.

Send a retroactive approval request as one list of linked tickets.
Group the list as "before the approval rule" and "after the approval rule".

## Step 5 — Reconcile each change

Check these items for each sampled change:

1. The stamp has a real ticket key.
2. The ticket exists.
3. An approver approved the ticket before the change.
4. The SQL matches what the ticket authorized.
5. The operator had a PIM activation.
   A v2 `v` stamp proves this.
   Confirm self-asserted stamps with the operator.

Use these approval rules:

- Find an approval comment on the ticket from an approver on the list.
  The comment date must be before the change.
- Do not use the Jira JSM Approvals fields.
  The team does not use them.
- Do not count triage, assignment, "Migration completed", or "We will review on Monday" as approval.
- Close a retroactive "Approved." comment as "approved after the fact".

Log each of these as an exception (E-n):

- No ticket, no approval, or approval after the change.
- An unknown operator, or an operator not on the authorized list.
- A change after the ticket was resolved.
  The corrective action is to reopen the ticket or to open a new ticket.
- A change outside the stamped ticket in the same session.
  Use one ticket per session.
- An unstamped session or a read-write console on the prod swarm.

Find tickets with `getJiraIssue`.
Business requests are often in Slack `#production-data-change-request`.

## Step 6 — Write the CSV evidence

Write these files:

- `pdcr-<YYYY-MM>-session-summary.csv`: one row per operator session.
  Columns: region, session id, timestamp, actor, attribution basis, ticket, disposition.
- `pdcr-<YYYY-MM>-direct-changes-detail.csv`: one row per in-scope statement.
  Columns: region, timestamp, session, db_user, app, class, command, object, statement, params.
- `pdcr-<YYYY-MM>-pim-activations.csv`: from Step 1.

## Step 7 — Create the cycle page

1. Create a child page under the runbook.
   Name it `Monthly Review — <Mon YYYY>`.
2. Copy the report template from runbook Step 4.
   Replace each `<…>` placeholder.
3. Keep the sections and their order.
   Do not add method notes or extra findings.
4. Ask the page owner to attach the CSV files in Edit mode.
   No API tool creates a file attachment.
5. Link each Exception row to its evidence.
   Use a Slack link or a Jira comment.

Read [references/confluence.md](references/confluence.md) before you edit a page.

## Step 8 — Get an independent sign-off

The reviewer must not be an operator in the review period.
An operator is a person who made a change that month.
Escalate for an alternate reviewer when no non-operator is available.
Do not self-attest.

1. Tag the reviewer in the `#pdcr-team` thread.
   Include the page link.
2. Point the reviewer to the Sign-off table.
3. Put the reviewer's name in the Summary table.

The reviewer signs with their own Atlassian login.
The mention and the page history are the signature.

## Step 9 — Update the Evidence Log

1. Add one row to runbook §6.
   Columns: Cycle, Reviewed, Reviewer, Population, Sampled, Exceptions, Evidence.
2. Keep exception detail on the cycle page.
3. Add a row to the runbook revision history.

Do not update POD2-2261.
That ticket is Done.
The cycle page and the Evidence Log hold the monthly evidence.

## Optional — Verify the result

Verify the population counts and the attribution with independent agents before sign-off.
Use the `multi-agent-verify` skill if you have it.

## Audit evidence rules

- Keep audit evidence minimal.
  Do not add side findings.
  Extra content causes more auditor questions.
- Do not put PII or PHI on a page or in a CSV.
- Name the residual gap for each cycle.
  An auditor reads a named gap as maturity.

Residual gaps by era:

- Before Sep 2026: no stamp.
  Attribution is manual through the SSH key chain.
- POD2-2233 (Sep 2026): v1 stamp.
  The ticket is certain.
  The identity is self-asserted.
- POD2-2337 (after 2026-10-27): v2 `v` stamp.
  Root or `medstack` on a PDCR host can forge a token.
  PIM activation has no approver.
  POD2-2253 accepted this gap, and this review is the compensating control.
