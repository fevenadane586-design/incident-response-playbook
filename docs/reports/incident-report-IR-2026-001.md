# Incident Report IR-2026-001

**Classification:** Fictional exercise for portfolio use
**Organization:** Northwind Demo Ltd (fictional)
**Report author:** Feven
**Report date:** 14 October 2026
**Severity:** High
**Status:** Closed

## 1. Executive summary

On 14 October 2026, an external attacker guessed the password of the `admin` account on the server `srv01` after seven failed attempts, then logged in successfully. Detection came from a rule that flags a successful login after repeated failures. The account was disabled, the attacker's IP was blocked, and the password was reset. No data loss was identified. The root cause was a weak password with no multi-factor authentication and no login lockout.

## 2. Incident details

| Item | Detail |
|---|---|
| Incident type | Brute force leading to account compromise |
| Affected system | srv01 (SSH server) |
| Affected account | admin |
| Attacker IP | 198.51.100.23 (documentation range, fictional) |
| First malicious event | 09:02:03 |
| Successful login | 09:02:18 |
| Detection | Security Log Dashboard rules: brute force and suspicious successful login |
| Related activity | 203.0.113.77 ran a password spraying attempt against 5 usernames at 10:30 to 10:34, with no success |

## 3. Timeline

| Time | Event | Source |
|---|---|---|
| 09:02:03 to 09:02:13 | Six failed logins for `root` from 198.51.100.23 | auth log |
| 09:02:15 | Failed login for `admin` from the same IP | auth log |
| 09:02:18 | **Successful login for `admin`** from the same IP | auth log |
| 09:03 | Alerts raised: brute force (T1110.001) and suspicious success (T1078) | Dashboard |
| 09:10 | Analyst reviews alerts and confirms the pattern in raw logs | Analyst |
| 09:14 | Source IP 198.51.100.23 blocked at the firewall | Analyst |
| 09:15 | `admin` account disabled and active sessions terminated | Analyst |
| 09:40 | Review of the host finds no new accounts, keys or scheduled tasks | Analyst |
| 10:05 | `admin` password reset, MFA enabled, account re-enabled | System owner |
| 10:30 to 10:34 | Password spraying from 203.0.113.77 against test, guest, oracle, ubuntu, postgres | auth log |
| 10:36 | Second IP blocked | Analyst |

## 4. Analysis

**Attack path.** The attacker first tried `root`, then switched to `admin`, which suggests they were working through a list of common usernames. The `admin` account accepted a password on the seventh attempt, which points to a weak or commonly used password. The whole attack took 15 seconds, so it was automated.

**Impact.** The attacker had interactive access as `admin` for about 57 minutes (09:02 to 09:15 before the account was disabled, plus the time to confirm there was no persistence). Host review found no changes, no new persistence and no outbound connections to unknown addresses. No data loss was identified. Because no full host forensics were run, this should be described as "no evidence of further activity", not as proof that nothing happened.

**Root causes.**

1. Weak password on a privileged account
2. No multi-factor authentication
3. No login lockout or rate limiting on SSH
4. SSH reachable from the internet with password login enabled

## 5. MITRE ATT&CK mapping

| Tactic | Technique | Evidence |
|---|---|---|
| Credential Access | T1110.001 Password Guessing | Seven failures in 15 seconds from one IP |
| Credential Access | T1110.003 Password Spraying | One IP failing against 5 usernames |
| Initial Access | T1078 Valid Accounts | Successful login as `admin` |

## 6. Response actions

**Containment:** blocked both attacker IPs, disabled `admin`, terminated sessions.
**Eradication:** checked for persistence and found none, reset the password.
**Recovery:** re-enabled `admin` with MFA, 14 days of increased monitoring.

## 7. Lessons learned

| What went well | What to improve |
|---|---|
| The correlation rule caught the successful login within about one minute | The attack succeeded because nothing slowed it down |
| Logs were complete enough to rebuild the timeline | A privileged account used a guessable password |
| Containment took 12 minutes from alert to block | No automated blocking of IPs with repeated failures |

## 8. Recommendations

| # | Actireports/incident-report-IR-2026-001.mdon | Owner | Priority |
|---|---|---|---|
| 1 | Require MFA for all privileged accounts | System owner | High |
| 2 | Disable SSH password login and use keys only | System owner | High |
| 3 | Add rate limiting or fail2ban-style blocking | Infrastructure | High |
| 4 | Restrict SSH to a VPN or allow-listed IPs | Infrastructure | Medium |
| 5 | Enforce a password policy that checks against breached passwords | IT | Medium |
| 6 | Rename or disable default account names such as `admin` | IT | Medium |
| 7 | Add an alert for any success after 3 or more failures to the on-call runbook | SOC | Medium |

## 9. Evidence

- Authentication log extract, 14 October 2026, 09:02 to 09:03
- Dashboard alert export (`alerts.csv`)
- Firewall change record for the IP block

*All names, IP addresses and events in this report are fictional and made for education.*
