# Nmap-Scripting-Engine


**Author:** Dashane James  
**Lab Environment:** [e.g. VMware Workstation | Kali Linux | Metasploitable 2]  
**Purpose:** [The goal of this repository is to use Nmap scripts for targeted vulnerability checks, bridging network scanning and vulnerability assesment.]
**Status:** 🟢 Active / 🟡 In Progress / 🔵 Completed

---

## 📋 Overview

[This repository documents combining the network scanning of Nmap with the task of vulnerabulity assesments. This creates a simpler way to both scan a network and identify vulnerabilities simultaneously which is very useful for a cybersecurity analyst or penetration tester."]

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

### 1. [Run default scripts against key Metasploitable ports.]
For this task I ran a Nmap -sC script scan on the target. By doing this it runs a default set of scripts that go beyond basic port scanning to actually probe and interact with the services running on open ports, collecting much more valuable information than a simple open/closed" or "version number" result. 

# Command used
[sudo nmap -sC -Pn --disable-arp-ping -n -p 21,22,80,445 192.168.79.130]


# Output
-sC runs Nmap's default script set, which is a specific collection of scripts that go beyond basic sanning to actively interact with each service, testing things like default credentials and known weak configurations. Against Metasploitable, this revealed several notable results on ports 21,22,80, and 445.

<img width="325" height="260" alt="Screenshot 2026-07-25 165902" src="https://github.com/user-attachments/assets/d6d7347b-8354-4f74-aefc-4dd39ccf9d6f" />

<img width="326" height="257" alt="Screenshot 2026-07-25 165937" src="https://github.com/user-attachments/assets/b4b80872-4c9e-4b54-a036-fe37bfeb5d13" />

**Findings:**

Port 21 (FTP): Anonymous logins are allowed, meaning no credentials are required to access the FTP server. The scan also confirmed both control and data communications are transmitted in plain text.

Port 22 (SSH): Host key fingerprints(DSA/RSA) were retrieved, useful for host verification but not a vulnerability on its own. 

Port 445 (SMB): Identified as Samba 3.0.20-Debian, confirming this is a Linux/Unix based SMB rather than a Windows based SMB. The scan also flagged "message_signing: disabled (dangerous, but default), a known weakness that leaves SMB traffic vulnerable to tampering and showed the scan was able to authenticate using a guest account.

These results show that -sC surfaces real configuration weaknesses like anonymous access and disabled security features.


### 2. [Check FTP for anonymous login vulnerability]
For this task I use a different type of command to specifically display vulnerabilities for FTP. This allows me to have a more targeted approach for researching vulnerabilities on the target machine.

# Command used
[sudo nmap --script ftp-anon -Pn --disable-arp-ping -n -p 21 192.168.79.130]


# Output
The output displays almost identically the same vulnerabilities found when running the -sC command except it trims the info down to only the indicated port 21. Results of ftp-anon script confirmed: Anonymous FTP login allowed (FTP code 230).

<img width="314" height="142" alt="Screenshot 2026-07-25 191012" src="https://github.com/user-attachments/assets/77048d28-0911-41be-9c80-03ae549e72fb" />


**Findings:**
I found that this command again confirms port 21 accepts anonymous logins with no credentials required, which is obviously a critical misconfiguration.

### 3. [Run SMB vulnerability scripts against port 445]
[I ran a script to search for vulnerabilities in SMB. This is another example of using scripts in Nmap to uncover vulnerabilities on specific ports on the target device] 


# Command used
[sudo nmap --script smb-vuln* -Pn --disable-arp-ping -n -p 445 192.168.79.130]

# Output
The smb-vuln* wildcard ran every available SMB vulnerability-check script against port 445. Two of those checks returned false (MS10-061 and MS10-054). This means the target is not vulnerable to those specific issues. A third script(smb-vuln-regsvc-dos) failed to execute and returned an error rather than a definitive result. 

<img width="323" height="257" alt="Screenshot 2026-07-25 221859" src="https://github.com/user-attachments/assets/751e88a7-650a-44d9-821b-cdcd8fa342af" />


```

**Findings:**

I found that not every script scan turns up a positive or definitive result. This run confirmed the target is not vulnerable to two specific known SMB exploits (MS10-061 and MS10-054), and the other script failed to execute rather than returning a clear answer. This is an important distinction: a "false" result means the vulnerability was tested and ruled, while this script error means the test was inconclusive and shouldn't be considered safe until further investigation. 




### 4. [Run the vuln category for a broad check]



# Output


**Findings:** 


### 5. [Document every vuln NSE finds with its severity.]



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
[-sC]
[-Pn]
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
