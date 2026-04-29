\# 🔥 Attack 02 — Port Scan (Nmap)



\## 1. Description



Simulate a port scanning attack using Nmap from the attacker machine (Kali Linux) targeting the Ubuntu victim.

Port scanning is commonly used during the \*\*reconnaissance phase\*\* to discover open services and potential attack vectors.



\---



\## 2. Detection Rule (Kibana)



\*\*Rule type:\*\* Threshold

\*\*Index:\*\* `logs-system.syslog-\\\*`



\### Query:



```kql

event.dataset: "system.syslog" AND message: ("UFW" OR "BLOCK" OR "connection")

```



\### Threshold:



\* \*\*20 events within 1 minute\*\*

\* \*\*Group by:\*\* None



\*\*Severity:\*\* Medium

\*\*Risk Score:\*\* 50



\---



!\[Detection Rule](./screenshots/05-rule\_port\_scan.png)



\## 3. MITRE ATT\&CK



| Field     | Value                             |

| --------- | --------------------------------- |

| Tactic    | Reconnaissance                    |

| Technique | T1046 — Network Service Discovery |



\---





\## 4. Attack Execution



\*\*Attacker:\*\* Kali Linux (10.10.1.130)

\*\*Target:\*\* Ubuntu 22.04 (10.10.1.129)

\*\*Tool:\*\* Nmap



\### Run scan:



```bash

nmap -sS -T4 -p- 10.10.1.129

```



\* `-sS`: SYN scan (stealthy)

\* `-T4`: faster execution

\* `-p-`: scan all ports



\---



!\[Nmap](./screenshots/01-nmap.png)



\## 5. Log Evidence



\*\*Location:\*\* `/var/log/syslog` on Ubuntu Target (Victim)



Example log:



```

kernel: \[UFW BLOCK] IN=eth0 SRC=10.10.1.130 DST=10.10.1.129 ...

```



\### Verify:



```bash

sudo tail -f /var/log/syslog

```



\---



!\[Syslog](./screenshots/02-syslog.png)



\## 6. Kibana Alert Triggered



!\[Alert](./screenshots/03-alert.png)

!\[Alert1](./screenshots/04-alert\_detail.png)



\---



\## 7. Analysis



\* High volume of connection attempts detected within a short period

\* Source IP identified as attacker machine (Kali)

\* Traffic pattern consistent with automated scanning tool

\* Firewall logs (UFW) clearly indicate blocked probing attempts



\### False Positives



\* Network monitoring tools may generate similar patterns

\* Threshold (20/min) helps reduce noise from normal traffic



