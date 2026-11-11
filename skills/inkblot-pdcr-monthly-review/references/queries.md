# PDCR review queries

Run each KQL query in both workspaces.
Set `start` to the first day of the review month.

```bash
az monitor log-analytics query -w <WORKSPACE_ID> --analytics-query "<KQL>" -o json
```

pgAudit writes to `AzureDiagnostics` with Category `PostgreSQLLogs`.
The log line prefix is `%m %u %a %c`: timestamp, DB user, application name, session id.
The session id is `<backend start epoch hex>.<pid hex>`.
The session id has no space before `LOG:`.
Example: `6ad7f4f7.85581LOG:`.
Use `contains` to match a session id, not `has`.

## Population query

This query keeps all operator sessions.
It removes app traffic, scheduled rake runs, and Azure or Postgres internal traffic.

```kusto
let start = startofmonth(datetime(2026-10-01));   // set to the review month
let end   = endofmonth(start);
AzureDiagnostics
| where TimeGenerated between (start .. end)
| where Category == "PostgreSQLLogs" and Message has "AUDIT:"
| extend f = split(Message, " ")
| extend db_user = tostring(f[3]), app_name = tostring(f[4])
| extend sess = extract(@"([0-9a-f]+\.[0-9a-f]+)LOG:", 1, Message)
| where not(app_name has "puma" or app_name has "sidekiq")
| where app_name !in ("[unknown]", "") and app_name != "/usr/local/bundle/bin/rake"
| where not(db_user startswith "azure" or db_user == "replication")
| extend audit = substring(Message, indexof(Message, "AUDIT:"))
| where not(audit matches regex @",(query_store|query_stats|cron)\.")
| project TimeGenerated, db_user, app_name, sess, audit
| order by TimeGenerated asc
```

Group the result by `sess`.
An empty `app_name` is Azure `query_store` maintenance.

Manual post-deploy rake runs look the same as scheduled rake runs.
The query removes both.
The PIM export identifies who ran them.
A one-off data-fix rake task still needs a PDCR ticket.
That ticket key must be the PIM justification.

## Stamp query

This query parses the v1 and v2 stamps.

```kusto
let start = startofmonth(datetime(2026-10-01));   // set to the review month
let end   = endofmonth(start);
AzureDiagnostics
| where TimeGenerated between (start .. end)
| where Category == "PostgreSQLLogs" and Message has "AUDIT:"
| extend f = split(Message, " ")
| extend db_user = tostring(f[3]), app_name = tostring(f[4])
| where app_name startswith "PDCR:"
| extend p = split(app_name, ":")
| extend fmt = iff(array_length(p) == 4, "v2", "v1")
| extend ticket   = iff(fmt == "v2", tostring(p[1]), tostring(p[2])),
         verified = iff(fmt == "v2", tostring(p[2]) == "v", false),
         operator = iff(fmt == "v2", tostring(p[3]), tostring(p[1]))
| extend sess = extract(@"([0-9a-f]+\.[0-9a-f]+)LOG:", 1, Message)
| extend audit = substring(Message, indexof(Message, "AUDIT:"))
| project TimeGenerated, db_user, app_name, fmt, operator, ticket, verified, sess, audit
| order by TimeGenerated asc
```

v1 stamps are cut at 63 bytes.
Paper Trail `whodunnit` uses the same data.
v1: `pdcr:<upn> (<ticket>)`.
v2: `pdcr:<ticket>:<v|s>:<upn>`.

## Session lookup

Use this query to find the source host and the duration of one session.

```kusto
let start = startofmonth(datetime(2026-10-01));   // set to the review month
let end   = endofmonth(start);
AzureDiagnostics
| where TimeGenerated between (start .. end)
| where Category == "PostgreSQLLogs"
| where Message contains "<SESSION_ID>LOG:"
| where Message contains "connection received"
     or Message contains "connection authorized"
     or Message contains "disconnection: session time"
| project TimeGenerated, Message
| order by TimeGenerated asc
```

- `connection received: host=<ip>` gives the source VM.
- `connection authorized: user=... application_name=...` gives the DB user and the app.
- `disconnection: session time: ...` gives the duration.

## Read-write console check

This query lists each Rails console that used the read-write credential.
It adds the source host and the duration.

```kusto
let start = startofmonth(datetime(2026-10-01));   // set to the review month
let end   = endofmonth(start);
let pg = AzureDiagnostics
| where TimeGenerated between (start .. end)
| where Category == "PostgreSQLLogs"
| extend sess = extract(@"([0-9a-f]+\.[0-9a-f]+)LOG:", 1, Message);
let consoles = pg
| where Message contains "connection authorized"
    and Message contains "user=inkblot4ever"
    and Message contains "application_name=bin/rails"
| project authorized_at = TimeGenerated, sess;
let hosts = pg
| where Message contains "connection received"
| project sess, host = extract(@"host=([0-9.]+)", 1, Message);
let ends = pg
| where Message contains "disconnection: session time"
| project sess, ended_at = TimeGenerated,
          duration = extract(@"session time: ([0-9:.]+)", 1, Message);
consoles
| join kind=leftouter hosts on sess
| join kind=leftouter ends on sess
| project authorized_at, sess, host, ended_at, duration
| order by authorized_at asc
```

Map each host to a VM.
Report a console that ran on a prod swarm node without a PDCR stamp.

## PIM activations

Read the activation requests for both database resource groups.
Read the subscription scope too.

```bash
SUB=$(az account show --query id -o tsv)
for scope in \
  "subscriptions/$SUB/resourceGroups/server-group-prod-438" \
  "subscriptions/$SUB/resourceGroups/server-group-prod-439" \
  "subscriptions/$SUB"; do
  az rest --method get \
    --url "https://management.azure.com/$scope/providers/Microsoft.Authorization/roleAssignmentScheduleRequests?api-version=2020-10-01" \
    -o json
done
```

Filter the requests to the review month on `properties.createdOn`.
Keep `principalId`, `requestType`, `status`, `justification`, `scheduleInfo`, and `createdOn`.
Cross-check with `az monitor activity-log list`.
Filter on the operation `roleAssignmentScheduleRequests/write`.
