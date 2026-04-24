\# 🔍 SIEM Detection Lab — Elastic Stack 8.x



![Elastic Stack](https://img.shields.io/badge/Elastic_Stack-8.19.14-005571?logo=elasticsearch)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE_ATT%26CK-5_Techniques-red)
![Platform](https://img.shields.io/badge/Platform-VMware-grey)
![Status](https://img.shields.io/badge/Status-Active-brightgreen)



\## Overview

A centralized SIEM lab built on Elastic Stack 8.x to detect, alert, and investigate 

real-world attack scenarios mapped to MITRE ATT\&CK framework.



\## Lab Architecture

!\[Topology](https://github.com/phuonguyen65/siem-detection-lab/blob/main/architecture/lab-topology.png?raw=true)



| Component | OS | IP | Role |

|---|---|---|---|

| Kali Linux | Rolling 2024 | 10.10.1.130 | Attacker |

| Ubuntu Target | 22.04 LTS | 10.10.1.129 | Victim |

| Elastic SIEM | 22.04 LTS | 10.10.1.128 | SIEM Server |



\## Detection Rules (MITRE ATT\&CK Mapped)



| Rule | Technique | Tactic | Severity |

|---|---|---|---|

| SSH Brute-Force Detection | T1110.001 | Credential Access | High |

| Successful SSH Login Monitor | T1078 | Initial Access | Low |

| Suspicious Privilege Escalation | T1548 | Privilege Escalation | Medium |

| New User Account Created | T1136.001 | Persistence | High |

| Port Scan Detection | T1046 | Discovery | Medium |



\## Screenshots



\### Alerts Overview

!\[Alerts](./screenshots/03-alerts-overview.png)



\### Brute-Force Alert Detail

!\[Alert Detail](./screenshots/04-brute-force-alert-detail.png)



\### Rules Enabled

!\[Rules](./screenshots/02-rules-enabled.png)



\### Log Stream in Discover

!\[Logs](./screenshots/05-discover-logs.png)



\### Fleet Agents Healthy

!\[Fleet](./screenshots/01-fleet-agents-healthy.png)



\## Attack Simulation

See \[attack-simulation/playbook.md](./attack-simulation/playbook.md) 

for step-by-step attack commands and expected detection behavior.



\## Importable Artifacts

\- \*\*Detection Rules\*\*: `detection-rules/rule-0x.ndjson` — importable directly to Kibana

\- \*\*Rule Documentation\*\*: `detection-rules/README.md`



\## What I Learned

\- How Elastic Agent ships structured logs vs raw syslog

\- Difference between Threshold rules and Custom Query rules

\- MITRE ATT\&CK mapping — connecting real attack behavior to framework techniques

\- Importance of false positive tuning: SSH brute-force threshold needed adjustment

\- Fleet Server architecture: how agents communicate through Fleet Server to Elasticsearch



\## Challenges \& Solutions



| Challenge | Solution |

|---|---|

| Elasticsearch failed to start | Conflicting `cluster.initial\_master\_nodes` with `single-node` mode — commented out the setting |

| Kibana not connecting to Elasticsearch | Output was using `http://localhost:9200` instead of `https://10.10.1.128:9200` |

| Elastic Agent not shipping logs | Fleet output URL was wrong — fixed to `https://10.10.1.128:9200` with `ssl.verification\_mode: none` |

| Port scan rule not triggering | Ubuntu syslog doesn't log Nmap directly — requires Network Packet Capture integration |



\## Tech Stack

\- \*\*Elasticsearch 8.19.14\*\* — log storage and search

\- \*\*Kibana 8.19.14\*\* — visualization and SIEM interface

\- \*\*Elastic Agent 8.19.14\*\* — log collection on endpoints

\- \*\*Fleet Server\*\* — centralized agent management

\- \*\*Hydra\*\* — SSH brute-force simulation

\- \*\*Nmap\*\* — port scanning simulation

