\# Attack Simulation Playbook — SIEM Detection Lab



\## Environment

| Machine | OS | IP | Role |

|---|---|---|---|

| Kali Linux | Rolling 2024 | 10.10.1.130 | Attacker |

| Ubuntu Target | 22.04 LTS | 10.10.1.129 | Victim |

| Elastic SIEM | 22.04 LTS | 10.10.1.128 | SIEM Server |



\---



\## Attack 1 — SSH Brute-Force (MITRE T1110.001)



\*\*Objective:\*\* Simulate password guessing attack against SSH service



\*\*Tool:\*\* Hydra



\*\*Commands (run on Kali):\*\*

```bash

\# Create password wordlist

cat > /tmp/passwords.txt << EOF

password

123456

admin

password123

letmein

root

toor

kali

qwerty

abc123

EOF



\# Run brute-force attack

hydra -l nvphuong -P /tmp/passwords.txt ssh://10.10.1.129 -t 4 -V

```



\*\*Expected Detection:\*\*

\- Rule triggered: `SSH Brute-Force Detection`

\- Severity: High

\- Condition: 5+ failed SSH logins from same source IP within 1 minute

\- Source IP in alert: `10.10.1.130` (Kali)



\*\*Result:\*\* ✅ Alert triggered successfully



\---



\## Attack 2 — Privilege Escalation via Sudo (MITRE T1548)



\*\*Objective:\*\* Simulate attacker attempting privilege escalation after gaining access



\*\*Commands (run on Kali):\*\*

```bash

\# SSH into target

ssh nvphuong@10.10.1.129



\# Attempt sudo with wrong password multiple times

sudo su

\# Enter wrong password 3-4 times

\# Ctrl+C to exit

exit

```



\*\*Expected Detection:\*\*

\- Rule triggered: `Suspicious Privilege Escalation - Sudo Failure`

\- Severity: Medium

\- Condition: Failed sudo authentication attempts



\*\*Result:\*\* ✅ Alert triggered successfully



\---



\## Attack 3 — Successful SSH Login (MITRE T1078)



\*\*Objective:\*\* Simulate attacker successfully logging in after reconnaissance



\*\*Commands (run on Kali):\*\*

```bash

ssh nvphuong@10.10.1.129

\# Enter correct password

exit

```



\*\*Expected Detection:\*\*

\- Rule triggered: `Successful SSH Login - Monitor`

\- Severity: Low

\- Condition: Any successful SSH authentication



\*\*Result:\*\* ✅ Alert triggered successfully



\---



\## Attack 4 — Create Backdoor User (MITRE T1136.001)



\*\*Objective:\*\* Simulate attacker creating persistence via new user account



\*\*Commands (run on target after gaining access):\*\*

```bash

ssh nvphuong@10.10.1.129

sudo useradd -m hacker123

sudo useradd -m backdooruser

exit

```



\*\*Expected Detection:\*\*

\- Rule triggered: `New User Account Created`

\- Severity: High

\- Condition: useradd command executed



\*\*Result:\*\* ⚠️ Rule created but requires tuning — useradd events

not consistently logged to system.auth on this Ubuntu version



\---



\## Attack 5 — Port Scanning (MITRE T1046)



\*\*Objective:\*\* Simulate reconnaissance port scan before attack



\*\*Tool:\*\* Nmap



\*\*Commands (run on Kali):\*\*

```bash

sudo nmap -sS 10.10.1.129

sudo nmap -A 10.10.1.129

sudo nmap -p 1-65535 10.10.1.129

```



\*\*Expected Detection:\*\*

\- Rule triggered: `Port Scan Detection`

\- Severity: Medium



\*\*Result:\*\* ⚠️ Limited detection — Ubuntu syslog does not log Nmap

activity directly. Full detection requires Network Packet Capture

integration (planned for Project 2 — Suricata IDS Lab)



\---



\## Detection Summary



| Attack | MITRE Technique | Rule Triggered | Result |

|---|---|---|---|

| SSH Brute-Force | T1110.001 | SSH Brute-Force Detection | ✅ |

| Privilege Escalation | T1548 | Suspicious Privilege Escalation | ✅ |

| Successful Login | T1078 | Successful SSH Login Monitor | ✅ |

| Create Backdoor User | T1136.001 | New User Account Created | ⚠️ |

| Port Scan | T1046 | Port Scan Detection | ⚠️ |



\## Notes

\- Lab environment: VMware Workstation, isolated Host-Only network

\- All attacks performed in controlled environment for educational purposes

\- Rules exported to `detection-rules/detection-rules.ndjson` for reuse

