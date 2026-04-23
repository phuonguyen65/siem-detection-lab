\# Detection Rules



All rules are mapped to MITRE ATT\&CK framework and importable to Kibana via `.ndjson` file.



\## Import Instructions

1\. Go to Security → Rules → Detection rules (SIEM)

2\. Click "Import rules"

3\. Select `detection-rules.ndjson`



\---



\## Rule 1 — SSH Brute-Force Detection

!\[Rule Overview](./rule-01-ssh-brute-force.png)

\- \*\*Type:\*\* Threshold

\- \*\*Severity:\*\* High | \*\*Risk Score:\*\* 73

\- \*\*MITRE Tactic:\*\* Credential Access

\- \*\*MITRE Technique:\*\* T1110.001 — Password Guessing

\- \*\*Logic:\*\* Alert when same source IP fails SSH login 5+ times within 1 minute

\- \*\*Index:\*\* `logs-system.auth-\*`

\- \*\*Query:\*\* `event.dataset: system.auth and event.action: ssh\_login and event.outcome: failure`

\- \*\*Why:\*\* Brute-force is one of the most common initial access techniques. Threshold of 5 balances detection sensitivity vs false positives from legitimate failed logins.



\---



\## Rule 2 — Successful SSH Login Monitor

!\[Rule Overview](./rule-02-ssh-login-monitor.png)

\- \*\*Type:\*\* Custom Query

\- \*\*Severity:\*\* Low | \*\*Risk Score:\*\* 21

\- \*\*MITRE Tactic:\*\* Initial Access

\- \*\*MITRE Technique:\*\* T1078 — Valid Accounts

\- \*\*Logic:\*\* Alert on every successful SSH login for investigation baseline

\- \*\*Index:\*\* `logs-system.auth-\*`

\- \*\*Query:\*\* `event.dataset: system.auth and event.action: ssh\_login and event.outcome: success`

\- \*\*Why:\*\* Complements brute-force rule — if attacker succeeds after many failures, this rule catches the successful entry.



\---



\## Rule 3 — Suspicious Privilege Escalation

!\[Rule Overview](./rule-03-privilege-escalation.png)

\- \*\*Type:\*\* Custom Query

\- \*\*Severity:\*\* Medium | \*\*Risk Score:\*\* 47

\- \*\*MITRE Tactic:\*\* Privilege Escalation

\- \*\*MITRE Technique:\*\* T1548 — Abuse Elevation Control Mechanism

\- \*\*Logic:\*\* Alert on failed sudo authentication attempts

\- \*\*Index:\*\* `logs-system.auth-\*`

\- \*\*Query:\*\* `event.dataset: system.auth and message: \*authentication failure\*`

\- \*\*Why:\*\* Repeated sudo failures indicate an attacker attempting to gain elevated privileges after initial access.



\---



\## Rule 4 — New User Account Created

!\[Rule Overview](./rule-04-new-user-created.png)

\- \*\*Type:\*\* Custom Query

\- \*\*Severity:\*\* High | \*\*Risk Score:\*\* 65

\- \*\*MITRE Tactic:\*\* Persistence

\- \*\*MITRE Technique:\*\* T1136.001 — Create Account: Local Account

\- \*\*Logic:\*\* Alert when new user account is created via useradd

\- \*\*Index:\*\* `logs-system.auth-\*`

\- \*\*Query:\*\* `event.dataset: system.auth and message: \*useradd\*`

\- \*\*Why:\*\* Attackers create backdoor accounts to maintain persistence after gaining access.



\---



\## Rule 5 — Port Scan Detection

!\[Rule Overview](./rule-05-port-scan.png)

\- \*\*Type:\*\* Custom Query

\- \*\*Severity:\*\* Medium | \*\*Risk Score:\*\* 47

\- \*\*MITRE Tactic:\*\* Discovery

\- \*\*MITRE Technique:\*\* T1046 — Network Service Discovery

\- \*\*Logic:\*\* Alert on syslog entries containing scan-related keywords

\- \*\*Index:\*\* `logs-system\*`

\- \*\*Query:\*\* `event.dataset: system.syslog and message: \*nmap\*`

\- \*\*Why:\*\* Port scanning is typically the first reconnaissance step before an attack.

