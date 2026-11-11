# Cycle page and Confluence edits

## Report template

Copy the template from runbook Step 4 (page `304480263`).
Keep these sections in this order:

1. Summary: Period, Date reviewed, Prepared by, Independent reviewer, Method.
2. Population: Region, Total audit entries, In scope, Reviewed.
3. Evidence line: the CSV files and the Slack thread link.
4. Reconciliation: #, When (UTC), Operator, Change, Ticket, Result.
5. Exceptions: #, What, Disposition, Status.
6. Sign-off: Reviewer, Role, Date, Signature.

Follow these rules:

- Link each ticket key to `https://inkblottherapy.atlassian.net/browse/<KEY>`.
- Use a real mention for each operator.
- Write Result as "✅ Authorized", "✅ Authorized · E-n", or "⚠ E-n".
- Write one Exceptions row for each E-n.
- Use a date field for the Sign-off Date.
- Put a tooling gap in a one-line note with its ticket link.
  A tooling gap is not an exception.

## Edit a page

`updateConfluencePage` replaces the full page body.
An HTML body can drop `mediaInline` nodes.
This breaks the attached CSV files.

Use Confluence REST v2 to edit a page with attachments:

1. Get the latest version.
   Use `GET /wiki/api/v2/pages/{id}?body-format=atlas_doc_format`.
2. Change only the target nodes in the ADF body.
3. Increase the version number by 1.
4. Send `PUT /wiki/api/v2/pages/{id}`.
   Set `body.representation` to `atlas_doc_format`.
5. Read the page again.
   Confirm that each `mediaInline` node is still there.

Always get the latest version before an edit.
People edit these pages by hand.
Do not remove mentions or media.

## HTML rules for the MCP tools

- Mention: `<span data-type="mention" data-user-id="<accountId>">@Name</span>`.
- Inline media: keep each `data-type="media-inline"` span the same.
- Code block: use `<pre><code>` with real newlines.
  `<pre><code class="language-sql">` also works.
  Do not use `<ac:structured-macro>`.
- Table: keep cells short.
  Use few columns.
  Put long lists below the table or in a `<details>` expand.
