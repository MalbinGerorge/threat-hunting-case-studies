# 🔍 VPN Authentication Failures – Threat Hunting Case Study

---

## 📌 Objective

Investigate abnormal authentication failures on a Cisco ASA device to determine whether the activity indicates brute-force attacks, password spraying, or username enumeration targeting VPN authentication services.

---

## 🧠 Hypothesis

If a high volume of authentication failures is observed from one or more source IP addresses, combined with attempts across a large number of usernames and consistent login patterns, then the activity may indicate automated credential access techniques such as brute force, password spraying, or username enumeration.

These behaviors can be identified by analyzing authentication logs for:

* Repeated authentication failures
* High number of unique usernames
* High login rate from a single source
* Consistent or automated login patterns
* Specific failure reasons (e.g., "User not found") indicating enumeration

---

## 🖥️ Environment

* **Device:** Cisco ASA (Firewall + VPN Gateway)
* **SIEM Platform:** Microsoft Sentinel
* **Log Source:** Syslog (AAA Authentication Logs)

---

## 🔍 Log Context

The analysis is based on the Cisco ASA syslog event:

`%ASA-6-113015: AAA user authentication Rejected`

This log indicates failed authentication attempts handled by the ASA device, which functions as both a firewall and VPN gateway.

Key fields used for analysis:

* **reason** → Indicates failure type (e.g., user not found, invalid password)
* **user** → Targeted username
* **user IP** → Source IP of the authentication attempt

This log is critical for identifying credential-based attacks such as brute force and username enumeration.

---

## 🔎 Detection Methodology

The investigation followed a hypothesis-driven approach:

1. Identify authentication failures
2. Extract and analyze source IP addresses
3. Measure login frequency per IP
4. Identify targeted usernames
5. Detect behavioral patterns (rate, distribution, automation)

---

## 🧪 Investigation Queries

All queries used in this investigation are available in:

📁 `queries/sentinel.kql`

---

## 🚨 Key Findings

### High Volume Authentication Attempts

Multiple source IPs generated extremely high authentication failures.

* Example: `10.x.x.x` → **11,777 attempts**

---

### Username Enumeration

* Over **5,000 unique usernames** attempted from a single IP
* Indicates use of large username wordlists

---

### Targeted Accounts

* The **admin** account was heavily targeted
* Suggests focus on privileged access

---

### Automated Attack Patterns

Time-based analysis revealed:

* Consistent login intervals
* Parallel attempts from multiple IPs
* High username variation

These indicators strongly suggest automated attack activity.

---

## 🛡️ MITRE ATT&CK Mapping

* **T1110 – Brute Force**
* **T1110.003 – Password Spraying**

---

## 🛡️ Detection Opportunities

Based on the findings, the following detection logic can be implemented:

* High number of authentication failures from a single IP within a short time window
* High count of unique usernames attempted from a single source
* Repeated failures targeting privileged accounts (e.g., admin)
* Consistent authentication patterns indicating automation

These detections can help identify credential-based attacks in real time.

---

## 🧾 Assessment

The observed activity strongly aligns with automated credential testing techniques.

However, all identified source IP addresses belong to internal network ranges, which introduces the possibility that the activity may originate from:

* Internal vulnerability scanners
* Security testing tools
* Misconfigured automation scripts

---

## 🏁 Conclusion

The investigation identified high-volume authentication failures involving multiple source IPs and a large number of username attempts.

Behavioral indicators—including high request frequency, username enumeration patterns, and consistent login intervals—strongly suggest automated credential-based attacks.

However, due to the internal nature of the source IPs, further validation is required to:

* Identify the systems generating the traffic
* Confirm whether the activity is legitimate or unauthorized

---

## 🔍 Scope Clarification

The logs analyzed originate from Cisco ASA, which functions as both a firewall and VPN gateway.

Based on the log type (AAA authentication failures) and behavioral patterns observed, the activity is most consistent with VPN authentication attempts rather than administrative access to the firewall.

---

## 📚 References

* Cisco ASA Syslog Message Guide
  https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/asa-syslog/syslog-messages-101001-to-199021.html#con_4769508

---

## 🧠 Learnings

* Behavioral patterns are more reliable than individual events
* Username enumeration is a strong early indicator of attacks
* Internal traffic can still represent potential security risks
* Log field analysis (e.g., failure reason) is critical for accurate classification

---
