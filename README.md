# Nmap-Scripting-Engine

**Author:** Dashane James  
**Lab Environment:** VMware Workstation | Kali Linux | Metasploitable 2  
**Purpose:** Use Nmap's built-in scripting engine to go beyond port scanning: actively probe services for misconfigurations, confirm known vulnerabilities, and in one case, demonstrate live exploitation of a critical CVE.  
**Status:** 🔵 Completed

---

## 📋 Overview

A port scan tells you what's open. The Nmap Scripting Engine (NSE) tells you what's wrong with it.

NSE lets you attach scripts directly to an Nmap scan that interact with open services, test for specific vulnerabilities, check configurations, and even, in select cases, attempt exploitation. This repository documents four progressively broader script scans against the same Metasploitable target used throughout this portfolio: default scripts (`-sC`), targeted service checks (`ftp-anon` and `smb-vuln*`), and a full vulnerability sweep (`--script vuln`) that produced the most significant finding of the entire week.

The `--script vuln` run is where this lab lands. Running it against port 21 didn't just detect the vsftpd 2.3.4 backdoor, it triggered it. The scan returned `uid=0(root) gid=0(root)`. At that point, reconnaissance had become confirmed compromise.

---

## 🧪 Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VMware Workstation (Host-Only Network) |
| Attacker Machine | Kali Linux 2026.1, `192.168.79.129` |
| Target Machine | Metasploitable 2, `192.168.79.130` |
| Network Type | Host-Only (isolated, no internet exposure) |
| Host OS | Windows 11, ASUS Vivobook 14 |

> ⚠️ **Note:** All activity was performed in a controlled, isolated lab environment against a deliberately vulnerable machine. No unauthorized access to live networks was performed.

---

## 🛠️ Tools Used

- **Nmap**: Used for both standard scanning and running NSE scripts to detect and, in one case, actively exploit vulnerabilities
- **NSE (Nmap Scripting Engine)**: Ran default (`-sC`), targeted (`ftp-anon`, `smb-vuln*`), and broad (`vuln`) script categories against the target
- **NVD (National Vulnerability Database)**: Referenced to research CVE details, CVSS scores, and attack vectors for confirmed vulnerabilities

---

## 🔬 Tasks / Assessments Performed

### 1. Run default scripts against key Metasploitable ports

The `-sC` flag runs Nmap's default script set, a curated collection that goes beyond basic port scanning to actively interact with each service. Default scripts test things like default credentials, anonymous access, and known configuration weaknesses without requiring you to name a specific script.

```bash
sudo nmap -sC -Pn --disable-arp-ping -n -p 21,22,80,445 192.168.79.130
```

<img width="325" height="260" alt="Screenshot 2026-07-25 165902" src="https://github.com/user-attachments/assets/d6d7347b-8354-4f74-aefc-4dd39ccf9d6f" />

<img width="326" height="257" alt="Screenshot 2026-07-25 165937" src="https://github.com/user-attachments/assets/b4b80872-4c9e-4b54-a036-fe37bfeb5d13" />

**Finding:**

The default scan returned meaningful results across all four ports.

**Port 21 (FTP):** Anonymous logins allowed with no credentials required to access the server. Both the control and data channels transmit in plaintext.

**Port 22 (SSH):** Host key fingerprints (DSA/RSA) were retrieved, which is useful for host verification but not a vulnerability on its own.

**Port 445 (SMB):** Identified as Samba 3.0.20-Debian, a Linux-based SMB implementation. The scan flagged `message_signing: disabled (dangerous, but default)`, meaning SMB traffic on this host is vulnerable to tampering and man-in-the-middle attacks. It also confirmed that guest authentication was allowed.

The `-sC` scan surfaced real configuration problems, anonymous FTP access and disabled SMB signing, without needing to target any specific vulnerability by name. Default scripts are a fast first look, and on this target, that first look was already revealing.

---

### 2. Check FTP for anonymous login

Rather than relying on what `-sC` happened to include, I ran a targeted script to check specifically for anonymous FTP access. This approach is useful when you want to confirm one particular misconfiguration with a clean, focused output.

```bash
sudo nmap --script ftp-anon -Pn --disable-arp-ping -n -p 21 192.168.79.130
```

<img width="314" height="142" alt="Screenshot 2026-07-25 191012" src="https://github.com/user-attachments/assets/77048d28-0911-41be-9c80-03ae549e72fb" />

**Finding:**

The `ftp-anon` script confirmed: **Anonymous FTP login allowed (FTP code 230)**. The output was narrower than the default scan, only port 21, only the anonymous login check, but the result was the same. Anonymous access to the FTP server requires no credentials at all.

This matters because it directly enables the attacker workflow: connect anonymously, browse the file system, and upload or retrieve files with no authentication barrier. Combined with the vsftpd 2.3.4 version confirmed in the Service Version Detection lab, this port represented both a misconfiguration and a critical CVE in one place.

---

### 3. Run SMB vulnerability scripts against port 445

The `smb-vuln*` wildcard runs every available SMB vulnerability-check script against the target in a single command rather than requiring each one to be named individually.

```bash
sudo nmap --script smb-vuln* -Pn --disable-arp-ping -n -p 445 192.168.79.130
```

<img width="323" height="257" alt="Screenshot 2026-07-25 221859" src="https://github.com/user-attachments/assets/751e88a7-650a-44d9-821b-cdcd8fa342af" />

**Finding:**

The wildcard ran all available SMB scripts. Two returned clear results: MS10-061 and MS10-054 both came back false, the target is not vulnerable to those specific exploits. A third script (`smb-vuln-regsvc-dos`) failed to execute and returned a script error rather than a definitive result.

There's an important distinction here. A `false` result means the vulnerability was tested and ruled out. A script error means the test was inconclusive, that's not a clean bill of health, it's an unresolved question. In a real assessment, inconclusive results require follow-up investigation rather than being treated as "not vulnerable."

This run also reinforced something useful: NSE scans don't always find something dramatic. Ruling out specific known vulnerabilities is itself a meaningful output. It narrows the scope of what needs investigation.

---

### 4. Run the `vuln` category for a broad vulnerability sweep

The `--script vuln` flag runs every vulnerability-detection script in Nmap's library across all open ports. This is the broadest NSE scan available, it covers dozens of scripts, runs against every open port, and produces significantly more output than any of the targeted scans above.

```bash
sudo nmap --script vuln -Pn --disable-arp-ping -n 192.168.79.130
```

<img width="320" height="257" alt="Screenshot 2026-07-25 234932" src="https://github.com/user-attachments/assets/10e2682f-50a8-4eb9-ad84-b1f56f23b7ea" />

**Finding:**

The output was substantially larger than any previous scan, scripts ran against nearly every open port rather than just the ones I specified. The most significant result came from port 21.

The `ftp-vsftpd-backdoor` script didn't just detect CVE-2011-2523, it triggered the backdoor live. It ran the `id` command through the backdoor channel and received:

```
uid=0(root) gid=0(root) groups=0(root)
```

This is confirmed remote code execution with root-level privileges. No credentials, no privilege escalation steps, no additional tools. The exploit was embedded in the vsftpd 2.3.4 source code itself, and Nmap's script triggered it in a single scan.

Most `vuln` category scripts only detect and report whether a vulnerability exists, they stop short of exploiting it because exploitation can crash services, generate alerts, or cause unintended consequences. The vsftpd backdoor is an exception: it's a well-documented, simple command-injection channel that can be reliably triggered and verified without destabilizing the service. That's why the script goes all the way to execution rather than stopping at detection.

The gap between "detected" and "exploited" is significant. This finding didn't need a CVE lookup or a CVSS score to communicate urgency. A root shell is the explanation.

---

### 5. Document every vulnerability NSE found with its severity

After the broad scan, I reviewed the full output and catalogued every result, confirmed vulnerabilities, informational findings, inconclusive results, and confirmed-not-vulnerable checks, by port, severity, and what each result actually means.

| Port/Service | Vulnerability | Severity | Notes |
|---|---|---|---|
| 21/tcp FTP | vsftpd 2.3.4 backdoor (CVE-2011-2523) | 🔴 Critical | Live exploitation confirmed, `uid=0(root) gid=0(root)`. CVSS 9.8. Remote code execution with no credentials. |
| 22/tcp SSH | Weak cryptographic configuration | 🟡 Medium | Weak or outdated cipher suites identified; may reduce the security of encrypted sessions. |
| 23/tcp Telnet | Telnet service running | 🟠 High | All session data, including credentials, transmitted in plaintext. No encryption. |
| 80/tcp HTTP | SQL injection detected | 🟠 High | SQL injection present on web application; could allow database read, write, or destruction. |
| 80/tcp HTTP | Slowloris DoS susceptibility | 🟡 Medium | Server appears susceptible to Slowloris-style connection exhaustion attacks. |
| 80/tcp HTTP | HTTP TRACE method enabled | 🟢 Low | TRACE enabled; low impact on its own but useful for attacker reconnaissance and may assist cross-site tracing. |
| 111/tcp rpcbind | RPC information disclosure | 🟢 Low | Exposes information about available RPC services, useful for attacker enumeration. |
| 139/tcp NetBIOS/SMB | SMB information disclosure | 🟡 Medium | Host and share information visible to unauthenticated queries. |
| 445/tcp SMB | MS10-061 | 🟢 Not Vulnerable | Script returned false, this specific exploit was tested and ruled out. |
| 445/tcp SMB | MS10-054 | 🟢 Not Vulnerable | Script returned false, ruled out. |
| 445/tcp SMB | smb-vuln-regsvc-dos | ⚪ Inconclusive | Script failed to complete, result is unresolved, not confirmed-safe. |
| 3306/tcp MySQL | Version and service info disclosed | 🟡 Medium | Database version exposed, useful for identifying applicable CVEs. |

**Finding:**

The full output shows why reviewing every result individually matters. A `false` on MS10-061 is a different kind of finding than the script error on `smb-vuln-regsvc-dos`, even though neither returned a confirmed vulnerability. And the vsftpd result sits in a different category entirely, not just a detected vulnerability but a confirmed exploit with proof of root access.

Running the broad vuln scan is not the same as understanding what it found. The output requires interpretation.

---

## 📊 Key Findings Summary

| Port/Service | Tool Used | Risk Level | Notes |
|---|---|---|---|
| 21/tcp FTP | Nmap (`vuln`, `ftp-anon`) | 🔴 Critical | vsftpd 2.3.4 backdoor (CVE-2011-2523), live exploitation confirmed, `uid=0(root)` returned |
| 23/tcp Telnet | Nmap (`vuln`) | 🟠 High | Transmits credentials in plaintext; vulnerable to interception |
| 80/tcp HTTP | Nmap (`vuln`) | 🟠 High | SQL injection vulnerability detected |
| 80/tcp HTTP | Nmap (`vuln`) | 🟡 Medium | Susceptible to Slowloris denial-of-service |
| 445/tcp SMB | Nmap (`-sC`, `smb-vuln*`) | 🟡 Medium | Message signing disabled; guest authentication allowed |
| 22/tcp SSH | Nmap (`vuln`) | 🟡 Medium | Weak/outdated cryptographic configuration |
| 3306/tcp MySQL | Nmap (`vuln`) | 🟡 Medium | Database version and service info exposed |

**Risk Levels:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low

---

## 🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| Default and targeted script scanning (`-sC`, `ftp-anon`, `smb-vuln*`) | Reconnaissance | T1595 | Active Scanning |
| Anonymous FTP login confirmed | Initial Access | T1078 | Valid Accounts |
| vsftpd backdoor exploitation, root shell via `id` command | Execution | T1059 | Command and Scripting Interpreter |
| Root-level access confirmed post-exploitation | Privilege Escalation | T1068 | Exploitation for Privilege Escalation |

---

## 🛡️ Defensive Recommendations

Based on findings, the following remediations would be recommended in a real environment:

1. **vsftpd 2.3.4 backdoor (Critical)**: Immediately uninstall vsftpd 2.3.4. This version contains an intentional backdoor and must never run in any environment. Replace with a patched FTP daemon or migrate to SFTP over SSH.
2. **Anonymous FTP login allowed (High)**: Disable anonymous access in the vsftpd configuration (`anonymous_enable=NO`). All FTP sessions should require authenticated accounts.
3. **Telnet enabled (High)**: Disable Telnet and replace with SSH for all remote administration.
4. **SMB message signing disabled (Medium)**: Enable SMB message signing to prevent tampering and man-in-the-middle attacks. Disable guest authentication. Restrict port 445 to authorized hosts.
5. **SQL injection on port 80 (High)**: Implement parameterized queries and input validation across all web application forms. Deploy a Web Application Firewall as an additional layer of defense.
6. **Inconclusive NSE results**: Any script that returned a failure or error rather than a definitive result (such as `smb-vuln-regsvc-dos`) should be retested with targeted tools before being considered resolved.

---

## 🔑 Technical Notes

```bash
# Task 1: Run default scripts (-sC) against key ports
sudo nmap -sC -Pn --disable-arp-ping -n -p 21,22,80,445 192.168.79.130

# Task 2: Targeted FTP anonymous login check
sudo nmap --script ftp-anon -Pn --disable-arp-ping -n -p 21 192.168.79.130

# Task 3: Run all SMB vulnerability scripts (wildcard) against port 445
sudo nmap --script smb-vuln* -Pn --disable-arp-ping -n -p 445 192.168.79.130

# Task 4: Broad vulnerability sweep across all open ports
sudo nmap --script vuln -Pn --disable-arp-ping -n 192.168.79.130

# NSE behavior note: not all vulnerability scripts work the same way.
# Most detect and report (true/false/error) without attempting exploitation.
# A small number, like ftp-vsftpd-backdoor, go further and attempt live
# exploitation when the vulnerability is well-documented and reliably
# triggerable without crashing the service. Always read script output
# carefully and don't assume uniform behavior across the vuln category.

# Wildcard note: --script smb-vuln* runs every NSE script whose name
# starts with "smb-vuln" in a single command. Useful for running an
# entire category without listing each script individually.

# -p requires a port value directly after it. If you accidentally
# write "-p" with no port, Nmap misreads the target IP as a port
# specification. To scan all ports, drop the -p flag entirely.

# Always add -n to prevent DNS resolution hangs on the isolated
# host-only network (no real DNS server is present to answer queries).
```

---

## 📌 About This Project

This repository is the final active scanning lab in the Week 1 reconnaissance portfolio, and it's where everything from the previous labs converged into a single confirmed result.

The port 21 vsftpd finding had been building across the entire week: the port was first identified on Day 1 (host discovery baseline), the vsftpd 2.3.4 version was confirmed on Day 3 (service version detection), the CVE-2011-2523 backdoor was researched and scored that same day (CVSS 9.8, Critical), and on Day 6 the NSE vuln scan triggered that backdoor and returned a root shell. A finding that started as "port 21 is open" ended as "confirmed remote code execution with root privileges." That chain, from baseline port list to live exploitation, is what this portfolio is designed to demonstrate.

**Related repositories (in order):**

- `Nmap-Host-Discovery-and-Lab-Baseline`: Established the baseline network scan that first identified port 21 as open, the starting point for everything this lab confirmed
- `Nmap-Scan-Types-SYN-vs-TCP-vs-UDP`: Confirmed the same port list through different scan types, including the full port scan that ensured no services were missed
- `Service-Version-Detection-and-OS-Fingerprinting`: Identified vsftpd 2.3.4 on port 21 and researched CVE-2011-2523, the vulnerability actively exploited in this lab
- `Wireshark-Capture-and-Analyze-Traffic`: Independently confirmed credential exposure on port 80 via HTTP, a finding that also showed up in the NSE vuln scan output
- `TCPDump-CLI-Packet-Capture`: Captured the Telnet session data that the NSE vuln scan confirmed as a high-risk plaintext service on port 23

---

## 👤 Author

**Dashane James**  
Senior Field Service Technician → Cybersecurity Analyst  
📍 Yonkers, NY  
🎓 B.S. Information Technology, SUNY Canton  
🏆 CompTIA Security+  
🔗 [GitHub](https://github.com/Dashanejames1) | [Zero Trust Cyber Security Brand](https://www.instagram.com/zerotrust_cybersecurity)

---

*This repository is part of an active portfolio demonstrating hands-on cybersecurity skills. All lab work performed in isolated environments for educational purposes.*
