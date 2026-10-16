# Playbook: Brute Force Attack with Account Compromise

**Version:** 1.0
**Scope:** SSH or remote login brute force, password spraying, and suspicious successful logins
**Based on:** NIST SP 800-61 incident handling phases
**Related ATT&CK techniques:** T1110.001 (Password Guessing), T1110.003 (Password Spraying), T1078 (Valid Accounts)

## Triggers

Start this playbook when any of these alerts fire:

- 5 or more failed logins from one IP within 5 minutes
- One IP failing against 4 or more different usernames
- A successful login right after 3 or more failures from the same IP

## Roles

| Role | Responsibility |
|---|---|
| Incident handler | Leads the response and keeps the timeline |
| System owner | Approves containment actions on the affected system |
| Communications lead | Informs management and, if needed, affected users |

## Phase 1: Detection and analysis

1. **Confirm the alert.** Open the raw logs and check the alert is real, not a user who mistyped a password.
2. **Scope it.** Find every account and system the source IP touched.
3. **Check for success.** Search for a successful login from the same IP. A success after failures means treat it as a confirmed compromise.
4. **Check what happened after login.** Look for new processes, new accounts, changed files, new scheduled tasks and outbound connections.
5. **Assign severity.**

| Severity | Condition |
|---|---|
| Low | Failures only, no success |
| Medium | Success on a low-privilege account, no further activity |
| High | Success on a privileged account, or any sign of post-login activity |

6. **Record the start time** and open an incident ticket.

## Phase 2: Containment

**Short term (do immediately):**

- Block the source IP at the firewall
- Disable or lock the compromised account
- Terminate active sessions for that account
- If the system may be tampered with, isolate it from the network (do not power off, so memory evidence is kept)

**Evidence preservation:**

- Copy the authentication logs and store them read-only
- Record the hash of each evidence file
- Note who collected it and when

## Phase 3: Eradication

- Reset the password of the compromised account to a new, strong, unique value
- Look for persistence: new users, SSH keys, scheduled tasks, startup items
- Remove anything the attacker added
- Check other systems where the same password was used and reset those too

## Phase 4: Recovery

- Restore the account with multi-factor authentication enabled
- Return isolated systems to the network after verification
- Monitor the account and the source IP range closely for 14 days
- Confirm the original alert no longer fires

## Phase 5: Post-incident activity

Hold a lessons-learned meeting within 5 business days and answer:

1. How was the attack detected, and how long did detection take?
2. Which control would have stopped it? (MFA, lockout, rate limiting, key-only SSH)
3. What should change in the detection rules or this playbook?

Write the final report and track every follow-up action to completion.

## Communication checklist

- [ ] Management informed
- [ ] Affected user informed and password reset confirmed
- [ ] Legal or privacy team consulted if personal data may be involved
- [ ] Final report delivered

## Decision points

| Question | If yes | If no |
|---|---|---|
| Was there a successful login? | Treat as confirmed compromise, go to containment | Block the IP and monitor |
| Was the account privileged? | Set severity High, escalate | Set severity Medium |
| Any sign of data access or persistence? | Escalate and consider external notification | Continue standard recovery |
