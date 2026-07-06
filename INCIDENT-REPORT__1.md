# Incident Report: Brute Force Login Attempt

## 1. Summary
On [date] at [time], host `ubuntu-target` recorded [n] SSH login attempts
against the `analyst` account from [attacker IP] within [duration] seconds.
[n-1] failed; the final attempt succeeded. The account is considered
compromised. Recommend immediate password reset and a block on the source IP.

## 2. Affected assets
- Host: `ubuntu-target`
- Account: `analyst`

## 3. Indicators of compromise
- Source IP: [attacker IP]
- Targeted user: `analyst`

## 4. Timeline
- [time] First failed login from [IP]
- [time] Further failed attempts ([n] total)
- [time] Successful login from same IP <-- compromise

## 5. Analysis
Wazuh rule 5712 (Multiple authentication failures) fired after [n] failed
logins from a single source IP within [duration] seconds. Reviewing the
events that followed showed a successful login (rule 5715) from that same
IP moments later. The burst-then-breakthrough pattern — repeated failures
followed immediately by a success from the identical source — is the
signature of a brute force attack that succeeded, not a legitimate user
mistyping their password. This distinguishes a routine failed-login alert
from a confirmed account compromise.

## 6. MITRE ATT&CK mapping
- T1110 Brute Force (Credential Access)
- T1078 Valid Accounts

## 7. Impact
The attacker gained valid credentials for the `analyst` account on
`ubuntu-target`, providing legitimate-looking access to the host. Depending
on the account's privileges, this could allow lateral movement, data access,
or further privilege escalation — and because it uses a valid account rather
than exploiting a vulnerability, this access is harder to distinguish from
normal activity.

## 8. Remediation
1. Force a password reset on `analyst` and review its recent activity.
2. Disable SSH password login; require SSH key-based authentication instead.
3. Install fail2ban to automatically block IPs after repeated failed attempts.
4. Ensure Wazuh rule 5712 is set to alert in real time so this is caught
   immediately in production.

## 9. Lessons learned
[What you'd detect or change to catch this sooner — e.g., a lower failure
threshold, alerting on rule 5712 directly, or requiring key-based auth from
the start to remove password-guessing as a viable attack path.]
