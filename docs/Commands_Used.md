# Commands Used in Task 1

> This document explains the commands used during Task 1 - Foundation & Environment Setup. Each command includes purpose, syntax, parameters, examples, expected output, troubleshooting guidance, and security notes.

## Command Summary

| Command | Category | Used For |
|---|---|---|
| `ip a` | Networking | IP address verification |
| `ifconfig` | Networking | Legacy IP verification on older systems |
| `ping` | Connectivity | Reachability testing |
| `nmap` | Reconnaissance | Port scanning |
| `nmap -sV` | Reconnaissance | Service version detection |
| `nmap -A` | Reconnaissance | Aggressive enumeration |
| `nmap -p-` | Reconnaissance | Full TCP port sweep |
| `nmap -sn` | Discovery | Host discovery |
| `ip route` | Networking | Route inspection |
| `ss -tulpen` | Local audit | Listening socket review |

## Workflow Context

The project workflow moved from setup to verification to reconnaissance. First, the virtual machines were attached to the same host-only network. Next, IP addresses were verified on Kali Linux and Metasploitable2. Then connectivity was tested with ping. Finally, Nmap scans were used to identify open ports and services on the intentionally vulnerable target.

## ip a

### Purpose

Display network interfaces, IPv4 addresses, IPv6 addresses, MAC addresses, and interface state.

### Syntax

```bash
ip a
```

### Parameters

`a` is short for `address` and asks the ip utility to show interface addresses.

### Example

```bash
ip a
```

### Output

Look for an interface with an address such as `192.168.56.101/24` on Kali or `192.168.56.102/24` on Metasploitable2.

### Troubleshooting

If no host-only address appears, confirm the VirtualBox adapter mode, restart networking, or reboot the VM.

### Security Notes

IP output can reveal internal network ranges. Avoid publishing sensitive production addresses.

## ifconfig

### Purpose

Display or configure network interfaces on older Linux systems.

### Syntax

```bash
ifconfig [interface]
```

### Parameters

An optional interface name limits output to one interface.

### Example

```bash
ifconfig eth0
```

### Output

Shows interface status, MAC address, IPv4 address, broadcast address, and packet counters.

### Troubleshooting

If the command is missing, use `ip a` or install legacy net-tools only when required.

### Security Notes

Treat interface data as infrastructure information; do not expose production details casually.

## ping

### Purpose

Test basic network reachability using ICMP echo requests.

### Syntax

```bash
ping [options] <target>
```

### Parameters

`-c 4` sends four packets on Linux; the target can be an IP address or hostname.

### Example

```bash
ping -c 4 192.168.56.102
```

### Output

Successful output shows replies, latency, packet loss, and summary statistics.

### Troubleshooting

If ping fails, verify IP addresses, subnet, adapter mode, firewall behavior, and whether the target is powered on.

### Security Notes

Ping is safe in a lab but should still be used only against authorized systems.

## nmap

### Purpose

Perform network reconnaissance by discovering hosts, ports, services, versions, and sometimes operating systems.

### Syntax

```bash
nmap [scan-options] <target>
```

### Parameters

Targets can be individual IPs, hostnames, ranges, or CIDR blocks.

### Example

```bash
nmap 192.168.56.102
```

### Output

Basic output lists open TCP ports, service names, host status, and scan timing.

### Troubleshooting

If results are empty, confirm target reachability, target IP, scan permissions, and firewall behavior.

### Security Notes

Nmap must only be used on systems you own or are explicitly authorized to test.

## nmap -sV

### Purpose

Detect service versions by probing open ports.

### Syntax

```bash
nmap -sV <target>
```

### Parameters

`-sV` enables version detection.

### Example

```bash
nmap -sV 192.168.56.102
```

### Output

Output includes service names and version banners when Nmap can identify them.

### Troubleshooting

Version detection may be slow or uncertain when services are filtered, customized, or unstable.

### Security Notes

Version data can reveal vulnerable software. Store scan results responsibly.

## nmap -A

### Purpose

Run aggressive discovery including service detection, OS detection, traceroute, and default scripts.

### Syntax

```bash
nmap -A <target>
```

### Parameters

`-A` enables several advanced features at once.

### Example

```bash
nmap -A 192.168.56.102
```

### Output

Output may include OS guesses, script results, traceroute, and detailed service information.

### Troubleshooting

If results are noisy or slow, run individual options such as `-sV` or `-O` separately.

### Security Notes

Aggressive scans are more intrusive and should be limited to authorized lab targets.

## nmap -p-

### Purpose

Scan all TCP ports from 1 to 65535.

### Syntax

```bash
nmap -p- <target>
```

### Parameters

`-p-` means every TCP port.

### Example

```bash
nmap -p- 192.168.56.102
```

### Output

Output lists open ports beyond the default top 1000 port set.

### Troubleshooting

Full scans take longer. Add timing options carefully only after basic connectivity is confirmed.

### Security Notes

Full-range scanning can be noisy and should be documented in rules of engagement.

## nmap -sn

### Purpose

Perform host discovery without a port scan.

### Syntax

```bash
nmap -sn <network>
```

### Parameters

`-sn` disables port scanning and focuses on host discovery.

### Example

```bash
nmap -sn 192.168.56.0/24
```

### Output

Output lists hosts that appear to be up.

### Troubleshooting

Some hosts block discovery probes. Try direct IP verification before assuming a host is down.

### Security Notes

Network sweep scans can still be considered reconnaissance; use only in scope.

## ip route

### Purpose

Display the kernel routing table.

### Syntax

```bash
ip route
```

### Parameters

No parameter is required for a basic route listing.

### Example

```bash
ip route
```

### Output

Shows directly connected networks, default gateway, interface names, and source addresses.

### Troubleshooting

If the target is off-subnet and no route exists, traffic will not reach it.

### Security Notes

Routes reveal network architecture and should be protected in real environments.

## ss -tulpen

### Purpose

List listening sockets and associated process information.

### Syntax

```bash
ss -tulpen
```

### Parameters

`-t` TCP, `-u` UDP, `-l` listening, `-p` process, `-e` extended, `-n` numeric.

### Example

```bash
ss -tulpen
```

### Output

Shows local ports, protocols, process names, and listening addresses.

### Troubleshooting

Some process details require elevated privileges. Use sudo when appropriate.

### Security Notes

Listening services define local attack surface. Review unexpected listeners.

## Practical Lab Sequence

```bash
ip a
ip route
ping -c 4 192.168.56.102
nmap 192.168.56.102
nmap -sV 192.168.56.102
nmap -A 192.168.56.102
```

This sequence avoids a common beginner mistake: scanning before confirming the target. When the IP address is wrong, scan output becomes misleading. When connectivity fails, a scanner cannot produce useful results. A disciplined analyst validates one layer at a time.

## Troubleshooting Table

| Symptom | Likely Cause | Fix |
|---|---|---|
| No IP address on Kali | Adapter disabled or DHCP issue | Check VirtualBox adapter and restart networking |
| Kali and target have different subnets | Wrong adapter or network | Attach both to same host-only network |
| Ping fails | Target down, wrong IP, firewall, wrong subnet | Verify power state, IP addresses, and routes |
| Nmap reports host down | Discovery probes blocked or wrong target | Try `-Pn` only after verifying scope |
| Version detection is incomplete | Service blocks probes or uses unusual banner | Try default scripts or manual banner checks |
| Full scan is slow | 65535 ports take time | Start with default scan, then expand |

## Security and Ethics

Every command in this document is legitimate when used in an owned or authorized environment. The same commands can become unauthorized reconnaissance if used against networks without permission. For internship documentation, keep the scope explicit: Kali Linux scanned Metasploitable2 inside a local VirtualBox host-only lab.

Good practice includes recording the target IP, date, command, output file, and purpose. This habit supports accountability and makes reports easier to review.


## Additional Command Usage Patterns

### Saving Nmap Output

Purpose: Preserve scan results for reporting and later analysis.

Syntax:

```bash
nmap -oN <normal-output-file> <target>
nmap -oA <basename> <target>
```

Parameters: `-oN` writes normal readable output. `-oA` writes normal, XML, and grepable formats using the same basename.

Example:

```bash
nmap -sV -oN task1-service-scan.txt 192.168.56.102
```

Output: A text file containing scan metadata, host status, open ports, and service details.

Troubleshooting: If the file is not created, check the current directory and write permissions.

Security Notes: Scan files may reveal vulnerable services and internal IP addresses. Do not publish production scan output without approval.

### Using -Pn

Purpose: Treat the target as online and skip host discovery.

Syntax:

```bash
nmap -Pn <target>
```

Parameters: `-Pn` is useful when ICMP or discovery probes are blocked.

Example:

```bash
nmap -Pn 192.168.56.102
```

Output: Nmap scans ports even if the host did not respond to discovery probes.

Troubleshooting: Do not use `-Pn` as a first step in a simple lab. First verify the IP address and adapter configuration.

Security Notes: `-Pn` can increase scan time across large ranges because every target is treated as alive.

### Using -T Timing Options

Purpose: Adjust scan timing.

Syntax:

```bash
nmap -T<0-5> <target>
```

Parameters: `-T0` is slowest and stealthiest; `-T3` is default; `-T4` is faster for reliable networks; `-T5` is aggressive.

Example:

```bash
nmap -T4 -sV 192.168.56.102
```

Output: Faster scan completion in a local lab.

Troubleshooting: If results become unreliable, reduce timing aggression.

Security Notes: Faster scans can be noisier and may stress fragile systems.

### Checking Local Listening Services

Purpose: Understand what the current machine is exposing.

Syntax:

```bash
ss -tulpen
```

Parameters: TCP, UDP, listening sockets, processes, extended details, numeric output.

Example:

```bash
sudo ss -tulpen
```

Output: A table of local listening sockets and associated processes.

Troubleshooting: Use `sudo` if process names are hidden.

Security Notes: Unexpected listeners should be investigated because they may represent unnecessary exposure.

### Reading Manual Pages

Purpose: Learn command options from local documentation.

Syntax:

```bash
man <command>
```

Example:

```bash
man nmap
man ip
```

Output: Manual page with description, options, examples, and references.

Troubleshooting: If manual pages are missing, install the relevant documentation package or use `--help`.

Security Notes: Reading documentation before running intrusive options reduces accidental misuse.

## Command Selection Logic

| Goal | Best First Command | Why |
|---|---|---|
| Identify local IP | `ip a` | Shows interface addresses directly |
| Confirm route | `ip route` | Shows network paths |
| Test reachability | `ping` | Quick connectivity signal |
| Discover common ports | `nmap target` | Fast initial scan |
| Identify versions | `nmap -sV target` | Adds service context |
| Run broader enumeration | `nmap -A target` | Combines OS, scripts, traceroute, versions |
| Preserve evidence | `nmap -oN file target` | Writes output to disk |

## Common Mistakes

| Mistake | Why It Causes Problems | Better Approach |
|---|---|---|
| Scanning the wrong IP | Results describe another host | Verify with `ip a` and ping |
| Using bridged networking for the target | Exposes vulnerable VM to LAN | Use host-only networking |
| Running aggressive scans first | Produces noisy output | Start basic, then add detail |
| Not saving output | Evidence is lost | Use `-oN` or `-oA` |
| Ignoring filtered states | Misses firewall context | Report uncertainty accurately |
| Publishing sensitive scan data | Reveals internal exposure | Sanitize or keep private |

## Evidence Template

Use a repeatable note format:

```text
Date:
Scanner:
Target:
Network:
Command:
Purpose:
Result Summary:
Screenshot:
Notes:
```

This template makes command evidence easier to review during internship evaluation and later portfolio discussion.

## Detailed Troubleshooting Playbooks

### Playbook 1: Kali has no host-only IP address

Purpose: Restore basic lab addressing so Kali can communicate with the target.

Syntax and commands:

```bash
ip a
ip link
sudo dhclient -v
ip route
```

Parameters: `ip link` shows interface state, `dhclient -v` requests an address from DHCP with verbose output, and `ip route` confirms whether the lab subnet is reachable.

Example output: A successful configuration should show an interface with an address such as `192.168.56.101/24`. The exact final octet may differ depending on DHCP leases.

Troubleshooting: If the interface is down, enable it from NetworkManager or restart the VM. If DHCP fails, check whether the VirtualBox host-only network has DHCP enabled. If static addressing is preferred, assign an address in the same subnet as Metasploitable2 and avoid address conflicts with the host adapter.

Security notes: Do not solve the issue by switching Metasploitable2 to bridged mode. That may make the vulnerable target reachable from the physical network.

### Playbook 2: Ping fails between Kali and Metasploitable2

Purpose: Identify whether the failure is caused by addressing, routing, adapter configuration, or host state.

Syntax and commands:

```bash
ip a
ip route
ping -c 4 192.168.56.102
arp -a
```

Parameters: `-c 4` limits ping to four packets. `arp -a` displays local neighbor cache entries.

Example output: Successful ping output includes replies with time values and a summary showing zero percent packet loss.

Troubleshooting: Confirm both VMs are attached to the same host-only adapter. Confirm both addresses are in the same CIDR range. Confirm the target is not paused or powered off. If the IP address changed after reboot, update the scan target.

Security notes: Ping is a diagnostic tool. In production environments, repeated ping sweeps can still be considered reconnaissance and should be scoped.

### Playbook 3: Nmap scan returns "host seems down"

Purpose: Decide whether the host is actually offline or discovery probes are blocked.

Syntax and commands:

```bash
ping -c 4 192.168.56.102
nmap -sn 192.168.56.102
nmap -Pn 192.168.56.102
```

Parameters: `-sn` performs discovery only. `-Pn` skips host discovery and treats the target as online.

Example output: If `-Pn` finds open ports after discovery failed, the target may block discovery probes but still accept TCP connections.

Troubleshooting: Use `-Pn` only after confirming the target IP is correct and within scope. If the wrong IP is scanned with `-Pn`, Nmap will waste time scanning a host that may not exist.

Security notes: `-Pn` can increase scan volume across large ranges. Use it carefully and document why it was necessary.

### Playbook 4: Service version detection is incomplete

Purpose: Improve service identification while keeping scan behavior controlled.

Syntax and commands:

```bash
nmap -sV 192.168.56.102
nmap -sV --version-intensity 5 192.168.56.102
nmap -sC -sV 192.168.56.102
```

Parameters: `--version-intensity` controls how many probes Nmap tries. `-sC` runs default scripts.

Example output: Improved output may include product names, versions, protocol details, and script findings.

Troubleshooting: Some services do not reveal banners, some are custom, and some close connections when probed. Manual validation may be required for important findings.

Security notes: Stronger probing is more active. In a lab this is acceptable, but in a professional assessment it should align with rules of engagement.

## Command Documentation Standard

For every command in a cybersecurity report, record the purpose, target, exact syntax, output summary, and interpretation. This prevents confusion later when multiple scans were run with different options. It also makes the work easier for a reviewer to reproduce.

| Documentation Field | Example |
|---|---|
| Command | `nmap -sV 192.168.56.102` |
| Purpose | Identify service versions on the target |
| Target | Metasploitable2 host-only IP |
| Result | Multiple open TCP services detected |
| Interpretation | Target exposes a broad vulnerable lab attack surface |
| Evidence | Screenshot and saved scan output |

## Security Notes for Command Usage

Commands are neutral; context determines whether usage is ethical. `nmap` is appropriate in this lab because Kali and Metasploitable2 are controlled by the learner. The same scan against a public IP without permission would be inappropriate. `ping` is a simple diagnostic, but a sweep across a network can still reveal active hosts. `ss` is local and defensive, but the output may reveal sensitive service names or ports.

Professional cybersecurity work requires authorization, minimal necessary testing, careful evidence handling, and accurate reporting. When documenting commands, avoid exaggeration. If a command identifies an open port, report the open port. Do not claim exploitation unless exploitation was actually performed and authorized.

## Final Command Checklist

| Before Running | Reason |
|---|---|
| Confirm scope | Prevent unauthorized testing |
| Confirm target IP | Avoid scanning the wrong system |
| Confirm network mode | Keep vulnerable systems isolated |
| Start with basic checks | Reduce noise and confusion |
| Save important output | Preserve evidence |
| Review results before reporting | Avoid false conclusions |

The commands in Task 1 are foundational because they teach a complete verification chain: identify addresses, test communication, enumerate ports, interpret services, and document evidence.
