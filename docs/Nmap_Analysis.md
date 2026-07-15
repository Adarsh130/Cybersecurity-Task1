# Nmap Analysis

> Professional reconnaissance analysis for Task 1 - Foundation & Environment Setup. Target: Metasploitable2. Scanner: Kali Linux. Network: VirtualBox host-only lab.

## Table of Contents

- [Nmap](#nmap)
- [Reconnaissance](#reconnaissance)
- [Host Discovery](#host-discovery)
- [Port Scanning](#port-scanning)
- [Version Detection](#version-detection)
- [OS Detection](#os-detection)
- [NSE](#nse)
- [Traces](#traces)
- [Ports Discovered](#ports-discovered)
- [Attack Surface](#attack-surface)
- [Risk Analysis](#risk-analysis)
- [Mitigation](#mitigation)
- [Best Practices](#best-practices)

## Nmap

Nmap, short for Network Mapper, is a widely used open-source tool for network discovery and security auditing. It can determine whether hosts are online, which ports are open, which services are listening, what versions may be running, and sometimes which operating system is present. In this project, Nmap is used for reconnaissance against Metasploitable2 inside an isolated VirtualBox host-only network.

Nmap is powerful because it supports many scan types. A basic scan gives a quick view of common TCP ports. A version scan probes services more deeply. Aggressive scanning combines several enumeration techniques. Script scanning uses the Nmap Scripting Engine to run safe checks, discovery scripts, or vulnerability-oriented scripts depending on the selected category.

## Reconnaissance

Reconnaissance is the process of collecting information before deeper testing. In ethical hacking, reconnaissance must be authorized and scoped. The purpose is to understand the target surface, not to cause disruption.

For Task 1, reconnaissance answered these questions:

- Is Metasploitable2 reachable from Kali Linux?
- Which TCP ports are open?
- Which services are exposed?
- Which service versions are visible?
- Which ports represent high-risk legacy or administrative access?
- What should be documented for future testing?

Reconnaissance is valuable because it reduces guesswork. Instead of assuming what a host runs, the analyst records observable evidence and uses that evidence to plan next steps.

## Host Discovery

Host discovery identifies whether a system appears to be online. In the lab, ping provided a simple connectivity test. Nmap can also perform discovery across a subnet:

```bash
nmap -sn 192.168.56.0/24
```

The `-sn` option disables port scanning and performs host discovery only. On local Ethernet-style networks, Nmap may use ARP discovery, which is reliable for local subnets. ICMP echo requests, TCP probes, and ARP behavior can all affect whether a host appears up.

If Nmap says a host is down, it does not always mean the host is powered off. Firewalls and filtering can block probes. In this lab, the screenshot evidence and ping test support that the target was reachable before scanning.

## Port Scanning

Port scanning identifies listening services. A TCP port can appear open, closed, or filtered.

| State | Meaning |
|---|---|
| Open | A service is accepting connections |
| Closed | The host replied but no service listens there |
| Filtered | A firewall or filter prevents Nmap from deciding |
| Open filtered | Nmap cannot determine open versus filtered |

Basic command:

```bash
nmap 192.168.56.102
```

Full TCP scan:

```bash
nmap -p- 192.168.56.102
```

The default scan is faster because it checks common ports. A full scan takes longer but can find services running on uncommon ports.

## Version Detection

Version detection probes open ports to identify applications and versions:

```bash
nmap -sV 192.168.56.102
```

This matters because risk depends on more than a port number. Port 22 may be low risk if OpenSSH is current and hardened, but high risk if the service is outdated, password authentication is weak, or access is unrestricted. Version detection can reveal old daemons associated with known vulnerabilities, but results should be validated before making strong claims.

## OS Detection

OS detection attempts to infer the target operating system from network behavior:

```bash
sudo nmap -O 192.168.56.102
```

Aggressive mode also includes OS detection:

```bash
nmap -A 192.168.56.102
```

OS detection is a guess based on fingerprints. It is useful for context but should not be treated as perfect. In this project, the target is known to be Metasploitable2, a vulnerable Linux VM, so OS detection supports rather than establishes the target identity.

## NSE

The Nmap Scripting Engine, or NSE, allows Nmap to run scripts for discovery, safe checks, default enumeration, authentication checks, and vulnerability research.

Common categories:

| Category | Purpose |
|---|---|
| default | Common safe scripts used by `-sC` |
| discovery | Gather additional information |
| safe | Designed to avoid disruption |
| auth | Authentication-related checks |
| vuln | Vulnerability checks |
| intrusive | More likely to affect target behavior |

Example:

```bash
nmap -sC -sV 192.168.56.102
```

Use NSE responsibly. Even in a lab, it is good practice to understand what a script does before running it.

## Traces

Traceroute-style output shows the path packets take to reach the target. In a host-only lab, the path should be very short because both VMs share a virtual network.

```bash
nmap --traceroute 192.168.56.102
```

In larger networks, traces can reveal routers, filtering points, and network segmentation. In this task, a short path confirms local lab communication rather than internet routing.

## Ports Discovered

Metasploitable2 commonly exposes many services. The exact screenshot output should be treated as the project evidence, while the table below explains the typical ports seen on this target and their security meaning.

| Port | Service | Typical Finding | Risk Level | Notes |
|---:|---|---|---|---|
| 21 | FTP | vsftpd or FTP service | High | Cleartext credentials and historically vulnerable versions |
| 22 | SSH | OpenSSH | Medium | Secure protocol but still sensitive administrative access |
| 23 | Telnet | Telnet daemon | Critical | Cleartext remote login |
| 25 | SMTP | Mail service | Medium | Can leak users, banners, relay behavior |
| 53 | DNS | Domain service | Medium | May expose records or zone transfer issues |
| 80 | HTTP | Web server | High | Web apps create broad attack surface |
| 111 | RPCBind | RPC mapper | High | Enables discovery of RPC services |
| 139 | NetBIOS | Samba/NetBIOS | High | File sharing enumeration and legacy risks |
| 445 | SMB | Samba | High | Shares, users, and remote vulnerabilities |
| 512 | exec | r-services | Critical | Legacy trust-based remote execution |
| 513 | login | rlogin | Critical | Legacy remote login |
| 514 | shell | rsh | Critical | Legacy remote shell |
| 1099 | Java RMI | RMI registry | High | Can expose Java remote object attack paths |
| 1524 | bindshell | Backdoor shell | Critical | Direct shell exposure in vulnerable labs |
| 2049 | NFS | Network File System | High | Export misconfiguration can expose files |
| 2121 | FTP-alt | Additional FTP service | High | Extra file transfer surface |
| 3306 | MySQL | Database | High | Sensitive data and weak credential risk |
| 5432 | PostgreSQL | Database | High | Sensitive data and remote access risk |
| 5900 | VNC | Remote desktop | High | Weak password can lead to GUI access |
| 6000 | X11 | X Window System | High | Remote display risks |
| 6667 | IRC | UnrealIRCd | Critical | Known vulnerable versions exist in training targets |
| 8009 | AJP | Apache JServ Protocol | Medium | Backend connector exposure |
| 8180 | HTTP-alt | Tomcat or web app | High | Management interfaces and web vulnerabilities |

## Attack Surface

The attack surface is the collection of reachable services, protocols, users, configurations, and software behaviors that could be targeted. Metasploitable2 intentionally has a large attack surface so learners can practice safely.

Attack surface observations:

- Multiple cleartext services are exposed.
- Administrative protocols are reachable from the lab network.
- Web services expose applications that may contain vulnerabilities.
- Databases listen on the network.
- Legacy Unix r-services may be enabled.
- File sharing and RPC services provide enumeration opportunities.
- Some services may disclose versions or banners.

In a real environment, every unnecessary open port increases monitoring, patching, and hardening requirements.

## Risk Analysis

| Risk | Cause | Impact | Priority |
|---|---|---|---|
| Credential disclosure | FTP, Telnet, r-services | Password interception or reuse | Critical |
| Remote shell exposure | Backdoor or legacy shell services | Direct system compromise | Critical |
| Weak remote administration | SSH, VNC, web managers | Unauthorized access | High |
| Database exposure | MySQL, PostgreSQL | Data theft or modification | High |
| Web vulnerabilities | HTTP services and apps | Injection, upload abuse, session attacks | High |
| Information disclosure | Banners, RPC, SMB, DNS | Easier targeting | Medium |
| Lateral movement | File sharing and trust services | Expansion after compromise | High |

Risk is evaluated by likelihood and impact. In Metasploitable2, many services are intentionally vulnerable, so the lab risk rating is high by design. In production, the appropriate response would be to remove unnecessary services, patch required services, enforce authentication, and segment the network.

## Mitigation

| Finding | Mitigation |
|---|---|
| FTP exposed | Replace with SFTP or FTPS; disable anonymous access |
| Telnet exposed | Disable Telnet; use SSH |
| Old service versions | Patch or upgrade packages |
| SMB exposed | Restrict access, disable SMBv1, audit shares |
| Databases exposed | Bind to localhost or private subnet; enforce strong auth |
| VNC exposed | Restrict by VPN or firewall; use strong passwords |
| HTTP apps exposed | Patch apps, add TLS, validate inputs, monitor logs |
| RPC/NFS exposed | Limit exports, restrict clients, disable unused services |
| Banners disclose versions | Reduce unnecessary banner detail where appropriate |
| Too many services | Apply least functionality and remove unused daemons |

Mitigation should be prioritized by exposure and business importance. A public-facing Telnet service is urgent. A lab-only training target is expected to remain vulnerable but must stay isolated.

## Best Practices

- Confirm authorization before scanning.
- Identify the exact target IP before running Nmap.
- Start with simple scans before aggressive scans.
- Save output using `-oN`, `-oX`, or `-oA`.
- Record date, scope, target, and command.
- Interpret ports in context; do not assume vulnerability from port number alone.
- Validate version findings before reporting exploitable issues.
- Keep vulnerable VMs isolated.
- Avoid scanning public networks from a training lab.
- Use screenshots as supporting evidence, not the only evidence.
- Separate observations, risks, and recommendations in reports.

## Example Reporting Language

Observation: The target host at `192.168.56.102` responded to network probes from Kali Linux and exposed multiple TCP services.

Risk: Several exposed services are legacy or commonly misconfigured, including FTP, Telnet, SMB, database services, and remote desktop services. If this were a production host, the exposed attack surface would require immediate review.

Recommendation: Disable unnecessary services, replace cleartext protocols with encrypted alternatives, restrict management ports, patch outdated software, and place sensitive systems behind network controls.

## Conclusion

The Nmap scans completed the reconnaissance portion of Task 1. The results demonstrate that the lab was correctly connected, the target was reachable, and multiple services were available for future controlled learning. The most important professional takeaway is that reconnaissance is not just running a tool; it is a structured process of verifying scope, collecting evidence, interpreting exposure, and communicating risk responsibly.


## Extended Nmap Methodology

A professional Nmap workflow is incremental. The analyst begins with scope confirmation, performs light discovery, runs a basic port scan, adds service detection, expands port coverage if needed, and only then considers scripts or aggressive options. This reduces noise and helps each result make sense.

### Suggested Scan Progression

| Phase | Command | Purpose |
|---|---|---|
| Scope check | `ip a` | Confirm scanner address |
| Reachability | `ping target` | Confirm basic connectivity |
| Discovery | `nmap -sn subnet` | Identify live lab hosts |
| Basic scan | `nmap target` | Find common open TCP ports |
| Version scan | `nmap -sV target` | Identify service versions |
| Default scripts | `nmap -sC -sV target` | Add safe common enumeration |
| Full ports | `nmap -p- target` | Find uncommon TCP listeners |
| Focused scripts | `nmap --script <script> -p <port> target` | Investigate specific services |

### Output Formats

Nmap supports multiple output formats. Screenshots are useful for visual evidence, but text and XML outputs are better for analysis.

| Option | Format | Use |
|---|---|---|
| `-oN` | Normal text | Human-readable report evidence |
| `-oX` | XML | Tool parsing and automation |
| `-oG` | Grepable | Legacy quick filtering |
| `-oA` | All major formats | Complete evidence capture |

Example:

```bash
nmap -sV -oA metasploitable2-service-scan 192.168.56.102
```

### Reading Port Lines

A typical Nmap port line has columns:

```text
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 4.7p1 Debian
```

The port and protocol identify the endpoint. The state describes how Nmap interpreted the response. The service is Nmap's best service label. The version is a banner or fingerprint result and may require validation.

### Service Enumeration Strategy

After Nmap identifies services, the analyst should group them:

| Group | Examples | Next Question |
|---|---|---|
| Remote login | SSH, Telnet, rlogin | Is authentication encrypted and restricted? |
| File transfer | FTP, SMB, NFS | Are anonymous or weak permissions present? |
| Web | HTTP, HTTP-alt, Tomcat | What applications and directories exist? |
| Databases | MySQL, PostgreSQL | Is remote access required and protected? |
| RPC and legacy | RPCBind, r-services | Can unnecessary services be disabled? |

This service grouping turns raw port lists into meaningful risk analysis.

### False Positives and Uncertainty

Nmap is excellent, but no scanner is perfect. Version detection can be wrong when services hide banners, use custom responses, or run behind proxies. OS detection can be uncertain on virtualized hosts. Firewalls can make open services appear filtered. Reports should use careful language such as "Nmap identified" or "appears to be" when results are not manually confirmed.

### Defensive Value of Nmap

Nmap is not only an offensive tool. Defenders use it to validate exposure, confirm firewall rules, find forgotten services, and audit segmentation. A system owner can scan an approved subnet and compare results against expected services. Unexpected ports become hardening tasks.

### Service Risk Deep Dive

FTP and Telnet are high-risk because they commonly transmit credentials in cleartext. SMB and NFS are high-value because file shares can expose sensitive data. Databases are high-impact because they may hold credentials, application data, or personal information. Web services are broad because application logic can create vulnerabilities even when the web server itself is patched.

| Service Type | Confidentiality Risk | Integrity Risk | Availability Risk |
|---|---|---|---|
| Cleartext login | Credential theft | Account misuse | Lockouts or abuse |
| File sharing | Data exposure | Unauthorized modification | Storage exhaustion |
| Database | Data theft | Data tampering | Query load or service crash |
| Web application | Session theft | Injection or upload abuse | Resource exhaustion |
| Remote desktop | Screen/data exposure | Full interactive control | Service disruption |

### Reporting Recommendations

A strong scan report separates observations from interpretation. An observation is what was seen: "Port 23/tcp was open." A risk explains why it matters: "Telnet transmits credentials without encryption." A recommendation says what to do: "Disable Telnet and use SSH with strong authentication."

Example:

```text
Observation: 23/tcp is open and identified as Telnet.
Risk: Telnet does not encrypt credentials or session traffic.
Recommendation: Disable Telnet and use SSH. Restrict remote administration to trusted networks.
```

### More Interview Questions

| Question | Answer Direction |
|---|---|
| What does `-sV` do? | It probes open ports for service version information. |
| What does `-A` include? | Aggressive features such as version detection, OS detection, default scripts, and traceroute. |
| Why might a port show filtered? | A firewall or packet filter prevents a clear response. |
| Why save XML output? | XML can be parsed by tools and used in automation. |
| What is NSE? | The Nmap Scripting Engine for scriptable checks and enumeration. |
| Is an open port always a vulnerability? | No. Risk depends on service, version, configuration, exposure, and context. |
| What is the danger of scanning without permission? | It can be illegal, disruptive, and unethical. |
| Why scan all ports? | Services may run outside the default top port list. |

### Lab-Specific Conclusion

The Metasploitable2 scan is intentionally noisy because the target was designed for practice. That is useful for learning, but it also reinforces the main safety rule of this repository: keep vulnerable systems isolated. Nmap helped prove that the lab is functioning and gave a structured view of the target's attack surface for future authorized exercises.

## Port-by-Port Professional Notes

### FTP on TCP 21

FTP is historically common for file transfer, but traditional FTP does not encrypt credentials or transferred data. In a lab, FTP is useful for learning banner grabbing, anonymous login checks, and file permission review. In production, FTP should generally be replaced with SFTP or FTPS, and anonymous access should be disabled unless there is a specific controlled business need.

### SSH on TCP 22

SSH is the preferred protocol for remote Linux administration because it encrypts traffic. However, an exposed SSH service still requires hardening. Analysts should consider password policy, root login settings, key-based authentication, allowed users, patch level, and network restrictions. SSH is not automatically a vulnerability, but it is an important management surface.

### Telnet on TCP 23

Telnet is high risk because it transmits usernames, passwords, and session content in cleartext. If Telnet appears in a real environment, it should usually be treated as a priority remediation item. In this lab, its presence helps demonstrate why encrypted administration protocols are necessary.

### SMTP on TCP 25

SMTP services can disclose mail server banners, usernames, supported commands, and relay behavior. Open relay testing must be handled carefully and only with permission. Even when not directly exploitable, SMTP information can support phishing risk analysis and infrastructure mapping.

### HTTP on TCP 80 and Alternate Web Ports

Web services are broad attack surfaces because risk may exist in the web server, framework, application logic, authentication, file upload handling, session management, or backend integration. Nmap identifies the service; web enumeration tools and manual review would be needed in later tasks to understand application-level vulnerabilities.

### RPC, SMB, and NFS Services

RPCBind, SMB, and NFS are important because they can reveal shares, exports, usernames, hostnames, and trust relationships. In production, these services should be restricted to trusted networks and monitored closely. Unnecessary file sharing services should be disabled.

### Database Services

MySQL and PostgreSQL ports represent high-value services because databases often store credentials, personal information, business records, and application data. Direct database exposure should be minimized. Strong passwords, least privilege accounts, network restrictions, patching, and audit logging are essential.

### Remote Desktop and GUI Services

VNC and X11 can provide visual or interactive access. Weak authentication or unrestricted network access can lead to serious compromise. These services should be limited to management networks or VPN access and protected with strong authentication.

## Risk Rating Method

Risk can be evaluated using likelihood and impact.

| Factor | Low | Medium | High |
|---|---|---|---|
| Exposure | Local only | Internal network | Public or broad network |
| Authentication | Strong MFA or keys | Password only | Weak, default, or absent |
| Encryption | Modern encryption | Mixed protection | Cleartext |
| Patch Level | Current | Slightly outdated | Known vulnerable |
| Business Impact | Limited data | Important service | Critical data or admin access |

Metasploitable2 would score high in many categories because it is designed for vulnerability practice. The correct lab control is isolation, not hardening the target into a normal server.

## Example Executive Summary

The target host was reachable from Kali Linux over the VirtualBox host-only network. Nmap identified multiple exposed services, including remote login, file transfer, web, database, file sharing, and legacy services. The number and type of open services indicate a large attack surface. Because this host is Metasploitable2, the exposure is expected and acceptable only inside the isolated lab. In a production environment, similar results would require urgent service review, patching, protocol replacement, and network restriction.

## Example Technical Finding Format

| Field | Content |
|---|---|
| Title | Cleartext remote login service exposed |
| Affected Host | 192.168.56.102 |
| Evidence | Nmap reported Telnet on TCP 23 |
| Risk | Credentials and commands may be transmitted without encryption |
| Impact | An attacker on the network could capture login data |
| Recommendation | Disable Telnet and use SSH with hardened authentication |
| Status | Lab observation |

This format keeps findings consistent and separates evidence from recommendation.

## Limitations of This Analysis

This project documents foundation setup and reconnaissance only. It does not include exploitation, credential attacks, vulnerability proof-of-concept execution, persistence, post-exploitation, or defensive monitoring. Nmap findings are interpreted as reconnaissance evidence. Any claim of vulnerability should be validated through authorized follow-up testing before being reported as exploitable in a real assessment.

The screenshot-based evidence is useful for internship review, but saved text output would be even better for long-term analysis. A future improvement would be to add sanitized Nmap output files in normal and XML formats.

## Final Analyst Notes

Nmap is most valuable when the analyst thinks beyond the command. The tool provides observations, but the analyst provides context. A port list becomes meaningful only when it is connected to protocol behavior, service purpose, exposure, authentication, encryption, version age, and business impact.

For this task, the key result is that the lab works: Kali can reach Metasploitable2, Nmap can enumerate services, and the findings are documented in a responsible way. That is the correct foundation for later cybersecurity internship tasks.
