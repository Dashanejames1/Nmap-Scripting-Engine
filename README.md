# Nmap-Scripting-Engine


**Author:** Dashane James  
**Lab Environment:** [e.g. VMware Workstation | Kali Linux | Metasploitable 2]  
**Purpose:** [The goal of this repository is to use Nmap scripts for targeted vulnerability checks, bridging network scanning and vulnerability assesment.]
**Status:** 🟢 Active / 🟡 In Progress / 🔵 Completed

---

## 📋 Overview

[This repository documents "]
0
---

## 🧪 Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VMware Workstation (Host-Only Network) |
| Attacker Machine | Kali Linux 2026.1 — `192.168.79.129` |
| Target Machine | [Metasploitable 2] — `192.168.79.130` |
| Network Type | Host-Only (isolated, no internet exposure) |
| Host OS | Windows 11 — ASUS Vivobook 14 |

> ⚠️ **Note:** All activity was performed in a controlled, isolated lab environment against deliberately vulnerable machines. No unauthorized access to live networks was performed.

---

## 🛠️ Tools Used

- **[Nmap]** — [What it does]
- **[Tool 2]** — [What it does]
- **[Tool 3]** — [What it does]
- **[Tool 4]** — [What it does]

---

## 📁 Repository Structure

```
[Repo-Name]/
├── README.md
├── [folder-1]/
│   ├── [file1.txt]        # Description of file
│   └── [file2.md]         # Description of file
├── [folder-2]/
│   ├── [file3.md]         # Description of file
│   └── [file4.txt]        # Description of file
└── reports/
    └── [final-report.md]  # Full assessment report
```

---

## 🔬 Tasks / Assessments Performed

### 1. [Run deafault scripts against key Metasploitable ports.]


# Command used
[sudo nmap -sC -Pn --disable-arp-ping -n -p 21,22,80,445 192.168.79.130]


# Output


**Findings:** 



### 2. [Check FTP for anonymous login vulnerability]
[Brief description of what you did and why]



# Output 



**Finding:**




---

### 3. [Run SMB vulnerability scripts against port 445]
[Brief description of what you did and why] 


# Command used
[sudo nmap --script ftp-anon -Pn --disable-arp-ping -n -p 21 192.168.79.130]

# Output .


```

**Findings:** [What did you discover?]




### 4. [Run the vuln category for a broad check]

# Command used
[sudo nmap --script smb-vuln* -Pn --disable-arp-ping -n -p 445 192.168.79.130]


# Output


**Findings:** 


### 5. [Run deafault scripts against key Metasploitable ports.]



# Output


**Findings:** 



---

## 📊 Key Findings Summary

| Port/Service | Tool Used | Risk Level | Notes |
|---|---|---|---|
| [e.g. 21/tcp FTP] | [e.g. Nmap] | 🔴 Critical | [Notes] |
| [e.g. 23/tcp Telnet] | [e.g. Wireshark] | 🔴 Critical | [Notes] |
| [e.g. 80/tcp HTTP] | [e.g. Nikto] | 🟠 High | [Notes] |
| [e.g. 3306/tcp MySQL] | [e.g. OpenVAS] | 🟠 High | [Notes] |
| [e.g. 22/tcp SSH] | [e.g. Nmap] | 🟡 Medium | [Notes] |

**Risk Levels:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low

---

## 🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| [e.g. Port scanning] | [e.g. Reconnaissance] | [e.g. T1595] | [e.g. Active Scanning] |
| [Action] | [Tactic] | [ID] | [Technique] |
| [Action] | [Tactic] | [ID] | [Technique] |
| [Action] | [Tactic] | [ID] | [Technique] |

---

## 🛡️ Defensive Recommendations

Based on findings, the following remediations would be recommended in a real environment:

1. **[Finding 1]** — [Recommendation]
2. **[Finding 2]** — [Recommendation]
3. **[Finding 3]** — [Recommendation]
4. **[Finding 4]** — [Recommendation]
5. **[Finding 5]** — [Recommendation]

---

## 📚 CySA+ Exam Relevance

This lab directly maps to the following CompTIA CySA+ (CS0-003) exam domains:

| Domain | Coverage |
|---|---|
| Security Operations (33%) | [What this lab covers in this domain] |
| Vulnerability Management (30%) | [What this lab covers in this domain] |
| Incident Response (20%) | [What this lab covers in this domain] |
| Reporting & Communication (17%) | [What this lab covers in this domain] |

---

## 🔑 Technical Notes

> [Any important notes about your lab setup, workarounds, or lessons learned. Example:
>
> "Always add the -n flag to Nmap scans in this VMware environment to prevent DNS resolution hangs."]
> 
> -sP and -sT contradict eachother (Can't be used together because -sP means just do a ping/host-discovery sweep, skip ports entirely but -sT tells Nmap to do a full TCP connect port scan. These commands contradict eachother.)

```bash
# Any important commands or workarounds
[command here]
```

---

## 📌 About This Project

[1-2 sentences about how this fits into your overall portfolio and career goals.]

**Related repositories:**
- `[Repo Name]` — [Brief description]
- `[Repo Name]` — [Brief description]
- `[Repo Name]` — Coming soon

---

## 👤 Author

**Dashane James**  
Senior Field Service Technician → Cybersecurity Analyst  
📍 Yonkers, NY  
🎓 B.S. Information Technology — SUNY Canton  
🏆 CompTIA Security+ | CySA+ (In Progress)  
🔗 [GitHub](https://github.com/Dashanejames1) | [Zero Trust Cyber Security Brand](https://www.instagram.com/zerotrust_cybersecurity)

---

*This repository is part of an active portfolio demonstrating hands-on cybersecurity skills. All lab work performed in isolated environments for educational purposes.*
