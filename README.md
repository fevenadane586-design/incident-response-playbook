# Incident Response Playbook and Sample Report

A documentation project showing how to respond to a brute force attack that ends in account compromise. It follows the NIST incident handling phases and maps the attack to MITRE ATT&CK.

The sample incident is built from the attack in my [Security Log Dashboard](https://github.com/fevenadane586-design/security-log-dashboard) sample data.

## Contents

| Document | Description |
|---|---|
| [Playbook: brute force with account compromise](docs/playbook-brute-force.md) | Reusable steps for detection, containment, eradication, recovery and lessons learned |
| [Incident report IR-2026-001](reports/incident-report-IR-2026-001.md) | Full report with timeline, analysis, ATT&CK mapping and recommendations |
| [Attack timeline](reports/attack-timeline.md) | Condensed timeline and key metrics |

## The scenario

An attacker guesses the `admin` password on a server after seven attempts and logs in. A correlation rule detects the successful login after repeated failures. The analyst blocks the IP, disables the account, checks for persistence, resets the password and enables MFA. A second source then runs a password spraying attempt, which fails.

## What I learned

- The NIST incident response lifecycle and what happens in each phase
- How to build a timeline from raw authentication logs
- How to separate evidence from assumptions (for example, "no evidence of further activity" is not the same as "nothing happened")
- How to map attacker behavior to MITRE ATT&CK
- How to write recommendations with owners and priorities

## Disclaimer

This is a fictional exercise. The organization, accounts, IP addresses and events are made up for education. IP addresses come from reserved documentation ranges.

## License

MIT
