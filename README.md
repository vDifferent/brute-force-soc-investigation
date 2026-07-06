# Brute Force Login Detection – SOC Investigation

A home-lab SOC investigation: detected and investigated an SSH brute force
attack that compromised an account, mapped it to MITRE ATT&CK, and produced
an incident report with remediation steps.

## Lab Architecture

Three isolated VMs on a private NAT network:

| Role | Machine | Purpose |
|------|---------|---------|
| Attacker | Kali Linux | Runs the brute force attack (Hydra) |
| Target | Ubuntu Server | Victim host, SSH exposed, Wazuh agent installed |
| SOC / SIEM | Wazuh | Collects logs, raises alerts, investigation console |

![architecture](screenshots/architecture.png)

## What I did

- Built an isolated lab: Ubuntu target, Kali attacker, Wazuh SIEM
- Simulated an SSH brute force attack against a deliberately weak account using Hydra
- Detected the attack in Wazuh (rule 5712 – Multiple authentication failures)
- Confirmed a successful login (rule 5715) from the same source IP immediately after the failures — indicating account compromise
- Mapped the activity to MITRE ATT&CK: **T1110 (Brute Force)** and **T1078 (Valid Accounts)**
- Wrote a Sigma detection rule describing the pattern
- Produced a SOC-style incident report with a timeline, impact assessment, and remediation plan

## Key screenshots

![failed logins](screenshots/failed-logins.png)
*Cluster of failed SSH authentication attempts from a single source IP*

![success alert](screenshots/success-alert.png)
*Successful login from the same source IP immediately following the failures*

![mitre panel](screenshots/mitre-panel.png)
*Wazuh's MITRE ATT&CK mapping for the alert*

## Detection rule

A portable Sigma rule describing the pattern, in [`detection/sigma.yml`](detection/sigma.yml).

## Full write-up

See [incident-report_1.md](incident-report_1.md) for the complete investigation,
including the five-question analysis (who, what, where, when, did it work),
MITRE mapping, and remediation recommendations.

## Tools used

`VirtualBox` · `Ubuntu Server` · `Kali Linux` · `Wazuh` · `Hydra` · `Sigma` · `MITRE ATT&CK`

## Lessons learned

The value isn't in running the attack tool — it's being able to say, with
evidence, whether the attacker actually got in, and what should change so it
doesn't happen again.
