# Attribute an unstamped session

Use this procedure for a `bin/rails` session or a self-asserted stamp.
Do it early in the cycle.
VM `auth.log` files stay for about 4 weeks.

## Steps

1. Find the session id in the population query result.
2. Run the session lookup query in [queries.md](queries.md).
   Get the source IP, the DB user, the app, and the session window.
3. Map the IP to a VM.

   ```bash
   az vm list-ip-addresses -o table | grep <ip>
   ```

4. SSH to that VM.
   Read the auth logs.

   ```bash
   sudo zcat -f /var/log/auth.log* | grep -E "Accepted publickey|session closed"
   ```

5. Find the SSH logins that cover the DB session window.
   The window must include the session start.
   A long-lived `rails console` can start hours before the change.
6. Map each key fingerprint to its owner.

   ```bash
   ssh-keygen -lf ~/.ssh/authorized_keys
   ```

7. Confirm the actor with the operator.
   Record the actor only after the operator confirms.

## Rules

- Use the client host from the `connection received` line.
  Do not assume the console host.
  A change can come from any app node.
- Mark the attribution "probable" when more than one key used the shared `medstack` login in the window.
  Keep it "probable" until the operator confirms.
- Get operator confirmation when no fresh SSH login exists.
  A persistent in-container process does not show a new login.
- Do not trust Paper Trail `whodunnit` alone.
  It is `nil` for models without actor versioning, such as `Company`.
  Paper Trail on `User` does not track password fields.
- Use read-only checks on `prod-1441-vm_debug` only as supporting evidence.
  Example: compare `updated_at` with the statement time.
  pgAudit does not log parameters.
  So pgAudit cannot prove the target row of an `UPDATE`.
- Treat a change to a test account as a prod DB change.
  Engineers change test accounts through the app, such as forgot password or the profile page.

## Log gaps

- No central auth log or sshd log exists.
- New Relic has only container logs.
- Log Analytics has only Postgres logs.
