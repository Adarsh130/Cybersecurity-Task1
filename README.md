# 🛡️ Cybersecurity & Ethical Hacking Internship
## 📌 Task 1 – Foundation & Environment Setup

<p align="center">
  <img src="https://img.shields.io/badge/Internship-ApexPlanet-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Platform-Kali%20Linux-success?style=for-the-badge">
  <img src="https://img.shields.io/badge/Virtualization-Oracle%20VirtualBox-orange?style=for-the-badge">
  <img src="https://img.shields.io/badge/Tool-Nmap-red?style=for-the-badge">
</p>

---

# 📖 Project Overview

This repository contains my submission for **Task 1 – Foundation & Environment Setup** of the **Cybersecurity & Ethical Hacking Internship** conducted by **ApexPlanet Software Pvt. Ltd.**

The primary objective of this task was to build a professional cybersecurity lab environment, understand Linux networking, configure isolated virtual machines, and perform basic reconnaissance using Nmap.

This lab provides a safe environment to practice penetration testing without affecting real-world systems.

---

# 🎯 Objectives

- Install Oracle VirtualBox
- Install Kali Linux
- Install Metasploitable2
- Configure an isolated Host-Only Network
- Understand Linux networking
- Verify communication between virtual machines
- Perform reconnaissance using Nmap
- Learn Linux fundamentals

---

# 🖥️ Lab Environment

| Component | Description |
|------------|-------------|
| Hypervisor | Oracle VirtualBox |
| Attacker Machine | Kali Linux |
| Target Machine | Metasploitable2 |
| Network | Host-Only Adapter |
| Scanner | Nmap |

---

# 🏗️ Architecture

```
                    Oracle VirtualBox

                ┌─────────────────────────┐
                │    Host-Only Network    │
                │     192.168.56.0/24     │
                └────────────┬────────────┘
                             │
        ┌────────────────────┴────────────────────┐
        │                                         │

┌─────────────────────┐               ┌────────────────────────┐
│     Kali Linux      │               │    Metasploitable2     │
│ 192.168.56.101      │               │ 192.168.56.102         │
│  Attacker Machine   │──────────────▶│ Vulnerable Target VM   │
└─────────────────────┘               └────────────────────────┘
```

---

# 🛠 Tools & Technologies

- Oracle VirtualBox
- Kali Linux
- Metasploitable2
- Linux Terminal
- Nmap
- Host-Only Networking

---

# 📦 Software Used

| Software | Purpose |
|----------|---------|
| Oracle VirtualBox | Virtualization Platform |
| Kali Linux | Penetration Testing OS |
| Metasploitable2 | Vulnerable Target Machine |
| Nmap | Network Scanning |

---

# 🌐 Network Configuration

### Kali Linux

```
IP Address : 192.168.56.101
Adapter    : Host-Only
```

### Metasploitable2

```
IP Address : 192.168.56.102
Adapter    : Host-Only
```

---

# ⚙️ Commands Executed

## Check Nmap Version

```bash
nmap --version
```

---

## Check Network Interfaces

```bash
ip a
```

---

## Verify Connectivity

```bash
ping 192.168.56.102
```

---

## Perform Advanced Scan

```bash
nmap -A 192.168.56.102
```

---

# 🔍 Nmap Scan Findings

The scan successfully identified multiple services running on the target machine.

| Port | Service | Description |
|------|----------|-------------|
| 21 | FTP | File Transfer Protocol |
| 22 | SSH | Secure Shell |
| 23 | Telnet | Remote Login |
| 25 | SMTP | Mail Service |
| 53 | DNS | Domain Name System |
| 80 | HTTP | Apache Web Server |
| 111 | RPC | Remote Procedure Call |
| 139 | NetBIOS | SMB |
| 445 | SMB | Windows File Sharing |
| 512 | rexec | Remote Execution |
| 513 | rlogin | Remote Login |
| 514 | shell | Remote Shell |
| 1099 | Java RMI | Java Remote Method Invocation |
| 1524 | bindshell | Vulnerable Service |
| 2049 | NFS | Network File System |
| 2121 | FTP | ProFTPD |
| 3306 | MySQL | Database |
| 5432 | PostgreSQL | Database |
| 5900 | VNC | Remote Desktop |
| 6000 | X11 | Display Server |
| 6667 | IRC | Internet Relay Chat |
| 8009 | AJP13 | Apache Connector |
| 8180 | Apache Tomcat | Web Application Server |

---

# 📷 Screenshots

The following screenshots are included inside the **screenshots/** folder.

- Oracle VirtualBox
- Kali Linux Desktop
- Metasploitable Login
- Kali Network Configuration
- Metasploitable Network Configuration
- Successful Ping Test
- Nmap Scan Output
- Host-Only Network Configuration

---

# 📚 Linux Commands Practiced

```bash
pwd

ls

cd

mkdir

touch

chmod

cp

mv

rm

ip a

ifconfig

ping
```

---

# 🎓 Key Learnings

Through this project I learned:

- Installing Kali Linux
- Installing Metasploitable2
- Virtual Machine Networking
- Host-Only Adapter Configuration
- Linux Networking
- Network Reconnaissance
- Host Discovery
- Service Enumeration
- Operating System Detection
- Using Nmap

---

# 📁 Repository Structure

```
Cybersecurity-Task1

│── README.md

│── screenshots
│     ├── virtualbox.png
│     ├── kali-desktop.png
│     ├── metasploitable-login.png
│     ├── kali-ip.png
│     ├── metasploitable-ip.png
│     ├── ping-success.png
│     ├── nmap-scan1.png
│     ├── nmap-scan2.png
│     └── nmap-scan3.png

│── Linux_CheatSheet.md

│── Commands_Used.md

│── Nmap_Analysis.md

│── Task1_Report.pdf
```

---

# ✅ Task Completion Checklist

- [x] Installed Oracle VirtualBox
- [x] Installed Kali Linux
- [x] Installed Metasploitable2
- [x] Configured Host-Only Network
- [x] Verified Connectivity
- [x] Installed Nmap
- [x] Performed Advanced Network Scan
- [x] Identified Running Services
- [x] Documented Findings

---

# 🚀 Future Scope

In the upcoming internship tasks I will explore:

- Vulnerability Assessment
- Wireshark
- Burp Suite
- Web Application Security
- SQL Injection
- Cross-Site Scripting (XSS)
- CSRF
- Metasploit Framework
- Exploitation Techniques
- Incident Response

---

# 👨‍💻 Author

## Adarsh Paswan

**B.Tech – Artificial Intelligence & Machine Learning**

Cybersecurity & Ethical Hacking Intern

📧 **Email:** adarshpaswan1106@gmail.com

🐙 **GitHub:** https://github.com/Adarsh130

💼 **LinkedIn:** https://www.linkedin.com/in/adarsh1306

---

# 🙏 Acknowledgements

I would like to thank **ApexPlanet Software Pvt. Ltd.** for providing this internship opportunity and a practical learning experience in cybersecurity and ethical hacking.

---

## ⭐ If you found this repository useful, feel free to give it a Star!
