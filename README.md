# 🔐 SOC Detection Lab - Elastic Stack 8.x

!\[Elastic Stack](https://img.shields.io/badge/Elastic\_Stack-8.19.14-005571?logo=elasticsearch)

!\[MITRE ATT\&CK](https://img.shields.io/badge/MITRE\_ATT%26CK-4\_Techniques-red)

!\[Platform](https://img.shields.io/badge/Platform-VMware-grey)

!\[Status](https://img.shields.io/badge/Status-Active-brightgreen)

!\[Elastic](./screenshots/01-elastic.png)

## 📌 Overview

This project simulates a **Security Operations Center (SOC)** environment using:

* **Kali Linux** as the attacker
* **DVWA (Damn Vulnerable Web Application)** as the victim
* **Elastic Stack (ELK + Elastic Agent/Filebeat)** for log collection, analysis, and detection

The goal is to:

* Generate real attack traffic
* Collect logs from the victim machine
* Build **detection rules in Kibana**
* Validate alerts in a SOC-like workflow

---

## 🧱 Architecture

```
Attacker (Kali) ───────▶ Victim (DVWA + Apache)
                              │
                              ▼
                        Elastic Agent
                              │
                              ▼
                        Elasticsearch
                              │
                              ▼
                            Kibana
```

!\[Topology](./screenshots/02-lab-topology.png)

---

## ⚙️ Environment Setup

### 1. Victim Machine

* OS: Ubuntu 22.04
* Services:

  * Apache2
  * MySQL
  * DVWA

### 2. Attacker Machine

* Kali Linux
* Tools:

  * `hydra`
  * `nmap`
  * browser (manual attack)

### 3. SIEM

* Elastic Stack 8.x
* Elastic Agent
* Fleet Server

---

## 📂 Log Sources

| Source            | Path                          |
| ----------------- | ----------------------------- |
| Apache access log | `/var/log/apache2/access.log` |
| Apache error log  | `/var/log/apache2/error.log`  |
| Auth log          | `/var/log/auth.log`           |

---

## 🚨 Attack Scenarios

### 🔴 Attack 01 – SSH Brute Force

**Tool:** Hydra
**Target:** SSH service

**Command:**

```bash
hydra -l root -P rockyou.txt ssh://10.10.1.129
```

**Log Source:**

* `/var/log/auth.log`

**Detection Rule:**

```kql
event.dataset: "system.auth" AND message: "Failed password"
```

---

### 🔴 Attack 02 – Port Scanning

**Tool:** Nmap

**Command:**

```bash
nmap -sS -p- 10.10.1.129
```

**Detection Logic:**

* High number of connection attempts

**Detection Rule:**

```kql
event.dataset: "system.syslog" AND message: ("nmap" OR "scan")
```

---

### 🔴 Attack 03 – SQL Injection (DVWA)

**Example Payload:**

```
1' UNION SELECT user,password FROM users #
```

**Log Example:**

```
GET /DVWA/vulnerabilities/sqli/?id=1%27+UNION+SELECT...
```

**Detection Rule:**

```kql
event.dataset: "apache.access" AND 
message: ("UNION SELECT" OR "information_schema" OR "OR 1=1")
```

---

### 🔴 Attack 04 – Web Shell Upload & Command Execution

**Step 1: Upload shell**

* DVWA File Upload

**Step 2: Execute**

```
shell.php?cmd=id
```

**Log Example:**

```
GET /DVWA/hackable/uploads/shell.php?cmd=id
```

**Detection Rule:**

```kql
event.dataset: "apache.access" AND message: ("cmd=" OR ".php?")
```

---

## Rules

!\[Rules](./screenshots/03-rules.png)

## 🧠 Detection Strategy

### Key Fields Used

| Field           | Purpose                     |
| --------------- | --------------------------- |
| `message`       | Main searchable log content |
| `event.dataset` | Identify log type           |
| `source.ip`     | Attacker IP                 |

---

## Alerts

!\[Alerts](./screenshots/04-alerts.png)

### ⚠️ Important Notes

* `event.original` **cannot be used in detection rules**
* Logs must be **indexed fields (e.g. message)**
* Alerts may have **delay (1–2 minutes)**

---

## 📊 Validation

Each attack was validated by:

1. Generating attack traffic
2. Confirming logs on victim
3. Verifying logs in Kibana
4. Triggering detection alerts

---

## 🧩 Challenges & Lessons Learned

* Apache logs may be parsed incorrectly if using wrong integration
* `event.original` is not searchable → must rely on `message`
* Filebeat must be **running continuously**
* Detection rules require tuning (avoid false positives)

---

## 🚀 Conclusion

This project demonstrates:

* End-to-end SOC workflow
* Realistic attack simulation
* Practical detection engineering in Elastic

It serves as a **foundation for SOC Analyst (L1–L2)** skills.

---

## 📌 Future Improvements

* Add MITRE ATT&CK mapping
* Build dashboards
* Add more attack scenarios (XSS, LFI, RCE)
* Implement alert correlation

---

