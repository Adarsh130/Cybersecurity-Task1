# Networking Basics

> A practical networking guide for cybersecurity lab setup, VirtualBox host-only networking, IP verification, connectivity testing, and Nmap reconnaissance.

## Table of Contents

- [Introduction](#introduction)
- [OSI Model](#osi-model)
- [TCP/IP Model](#tcpip-model)
- [IPv4](#ipv4)
- [IPv6](#ipv6)
- [Subnetting](#subnetting)
- [CIDR](#cidr)
- [Ports](#ports)
- [Protocols](#protocols)
- [Routing](#routing)
- [Switches](#switches)
- [Routers](#routers)
- [DNS](#dns)
- [DHCP](#dhcp)
- [NAT](#nat)
- [Host-only Networking](#host-only-networking)
- [VirtualBox Networking](#virtualbox-networking)
- [Firewall](#firewall)
- [VPN](#vpn)
- [Examples](#examples)
- [Diagrams](#diagrams)
- [Interview Questions](#interview-questions)

## Introduction

Networking is the foundation of cybersecurity because almost every modern system communicates with other systems. A penetration tester must understand how devices identify each other, how traffic moves, which services listen on which ports, and how defensive controls shape access. Task 1 uses a small but realistic lab: Kali Linux communicates with Metasploitable2 through a VirtualBox host-only network, and Nmap is used to identify exposed services.

The goal is not only to make two machines ping each other. The goal is to understand why they can communicate, what the IP addresses mean, how the subnet works, why isolation matters, and how port scanning converts network reachability into actionable security information.

## OSI Model

The OSI model is a conceptual framework with seven layers. It helps analysts break complex communication into smaller responsibilities.

| Layer | Name | Function | Example |
|---:|---|---|---|
| 7 | Application | User-facing network services | HTTP, DNS, FTP |
| 6 | Presentation | Data format, encoding, encryption | TLS, JPEG, UTF-8 |
| 5 | Session | Session management | Login sessions, RPC sessions |
| 4 | Transport | End-to-end delivery | TCP, UDP |
| 3 | Network | Logical addressing and routing | IP, ICMP |
| 2 | Data Link | Local network delivery | Ethernet, MAC addresses |
| 1 | Physical | Electrical/radio/physical media | Cables, signals |

In this project, `ping` uses ICMP at the network layer, Nmap often probes TCP and UDP at the transport layer, and discovered services such as FTP, SSH, and HTTP operate at the application layer.

## TCP/IP Model

The TCP/IP model is the practical model used by internet systems. It is often represented with four layers.

| TCP/IP Layer | Related OSI Layers | Examples |
|---|---|---|
| Application | 5-7 | HTTP, SSH, DNS, FTP |
| Transport | 4 | TCP, UDP |
| Internet | 3 | IP, ICMP |
| Network Access | 1-2 | Ethernet, Wi-Fi |

TCP provides reliable, connection-oriented delivery using a handshake and sequence tracking. UDP provides connectionless delivery with less overhead. Nmap scan behavior depends heavily on whether a service uses TCP or UDP because TCP ports respond differently from UDP ports.

## IPv4

IPv4 addresses are 32-bit numbers written as four decimal octets, such as `192.168.56.102`. Each octet ranges from 0 to 255. IPv4 addresses are split into network and host portions using a subnet mask or CIDR prefix.

Private IPv4 ranges are reserved for internal networks:

| Range | CIDR | Typical Use |
|---|---|---|
| 10.0.0.0 - 10.255.255.255 | 10.0.0.0/8 | Large private networks |
| 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 | Medium private networks |
| 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 | Home and lab networks |

The VirtualBox host-only default often uses `192.168.56.0/24). In that subnet, addresses such as `192.168.56.101` and `192.168.56.102` are on the same local network.

## IPv6

IPv6 addresses are 128-bit addresses written in hexadecimal groups, such as `fe80::a00:27ff:fe12:3456`. IPv6 was created to solve IPv4 exhaustion and improve address planning. A cybersecurity analyst should recognize IPv6 because systems may expose services over IPv6 even when only IPv4 is being tested.

Important IPv6 concepts:

| Concept | Meaning |
|---|---|
| Link-local | Starts with `fe80::/10); used on local links |
| Global unicast | Publicly routable IPv6 address |
| Loopback | `::1`, equivalent to IPv4 `127.0.0.1` |
| No broadcast | IPv6 uses multicast instead of broadcast |
| Neighbor Discovery | IPv6 equivalent for local address resolution |

In a beginner lab, IPv4 is usually simpler, but ignoring IPv6 in real assessments can miss exposed services.

## Subnetting

Subnetting divides a larger network into smaller networks. A subnet mask determines which part of the address identifies the network and which part identifies hosts. For example, `255.255.255.0` means the first three octets are the network portion and the last octet is the host portion.

For `192.168.56.0/24`:

| Item | Value |
|---|---|
| Network address | 192.168.56.0 |
| Usable host range | 192.168.56.1 - 192.168.56.254 |
| Broadcast address | 192.168.56.255 |
| Subnet mask | 255.255.255.0 |
| Number of usable hosts | 254 |

Kali and Metasploitable2 must be in the same subnet to communicate directly through the host-only adapter. If Kali is `192.168.56.101/24` and the target is `192.168.57.102/24`, they are in different networks and communication will fail unless routing exists.

## CIDR

CIDR stands for Classless Inter-Domain Routing. It writes the subnet mask as a prefix length. The prefix length tells how many bits are used for the network.

| CIDR | Subnet Mask | Usable Hosts |
|---|---|---:|
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /30 | 255.255.255.252 | 2 |

Nmap accepts CIDR ranges. For example:

```bash
nmap -sn 192.168.56.0/24
```

This performs host discovery across the lab subnet. Only run range scans on networks you own or are authorized to test.

## Ports

Ports identify services on a host. IP addresses identify systems; ports identify processes or services on those systems. TCP and UDP each have port numbers from 0 to 65535.

| Range | Name | Notes |
|---|---|---|
| 0-1023 | Well-known ports | Common services such as SSH, HTTP, DNS |
| 1024-49151 | Registered ports | Assigned to applications |
| 49152-65535 | Dynamic/private ports | Temporary client-side ports |

Common ports:

| Port | Protocol | Service |
|---:|---|---|
| 21 | TCP | FTP |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 139 | TCP | NetBIOS |
| 443 | TCP | HTTPS |
| 445 | TCP | SMB |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |

Open ports are not automatically vulnerabilities, but they are part of the attack surface. A secure system exposes only required services, keeps them patched, restricts access, and monitors usage.

## Protocols

Protocols define communication rules. Security analysis often begins by identifying which protocols are present.

| Protocol | Layer | Security Notes |
|---|---|---|
| ICMP | Network | Used by ping; may be filtered |
| TCP | Transport | Reliable, connection-oriented |
| UDP | Transport | Fast, connectionless, harder to scan |
| HTTP | Application | Unencrypted web traffic |
| HTTPS | Application | HTTP protected with TLS |
| SSH | Application | Encrypted remote administration |
| FTP | Application | Cleartext file transfer unless protected |
| Telnet | Application | Cleartext remote shell; insecure |
| DNS | Application | Name resolution; can leak infrastructure |
| SMB | Application | File sharing; high-value enumeration target |

Protocol choice affects security. Telnet and FTP are risky because credentials can be transmitted without encryption. SSH and HTTPS are preferred because they provide cryptographic protection when configured correctly.

## Routing

Routing moves packets between networks. A host uses its routing table to decide where to send traffic. Local subnet traffic goes directly to the destination at Layer 2; off-subnet traffic goes to a gateway.

```bash
ip route
```

Example output:

```text
192.168.56.0/24 dev eth0 proto kernel scope link src 192.168.56.101
default via 10.0.2.2 dev eth1
```

In a pure host-only lab, a default route may not be required for Kali to reach Metasploitable2. They only need to share the same subnet and adapter.

## Switches

A switch connects devices within the same local network. It forwards Ethernet frames based on MAC addresses. In a virtual environment, VirtualBox provides software switching behavior between VMs attached to the same host-only network.

Switching concepts matter because local network attacks and defenses often depend on ARP, MAC addresses, VLANs, and broadcast domains. Even in a small lab, `ip neigh` can show neighbor relationships learned through ARP.

## Routers

A router connects different networks. It forwards packets based on IP addresses. Home routers often combine routing, switching, DHCP, NAT, DNS forwarding, and firewall features. In this project, routing is intentionally minimized because the target should remain inside the host-only network.

If two VMs are in different subnets and cannot ping each other, a missing route may be the cause. However, for Task 1, the best solution is usually to attach both to the same host-only adapter rather than adding unnecessary routing.

## DNS

DNS maps names to IP addresses. Humans prefer names such as `example.com`; computers route using IP addresses. DNS can reveal subdomains, mail servers, name servers, and internal naming conventions.

Useful commands:

```bash
dig example.com
nslookup example.com
host example.com
cat /etc/resolv.conf
```

In the Task 1 lab, DNS is less important because scanning is performed directly against the target IP. In larger assessments, DNS reconnaissance is often one of the first steps.

## DHCP

DHCP automatically assigns IP settings such as address, subnet mask, gateway, and DNS server. VirtualBox host-only networks can provide DHCP if enabled. Static addresses can also be used for predictable labs.

| DHCP Item | Purpose |
|---|---|
| IP address | Host identity on the network |
| Subnet mask | Defines local network |
| Default gateway | Router for other networks |
| DNS server | Name resolution |
| Lease time | How long assignment remains valid |

If a VM does not receive an IP address, check the adapter mode, DHCP settings, interface state, and guest operating system network service.

## NAT

NAT translates private addresses to another address, commonly allowing internal hosts to access the internet through one public address. VirtualBox NAT is useful when a VM needs internet access for updates, but NAT is not ideal for exposing a vulnerable target to the lab scanner because inbound connectivity can be limited or port-forwarded in confusing ways.

For this task:

| Mode | Use |
|---|---|
| NAT | Optional temporary updates for Kali |
| Host-only | Main lab communication between Kali and Metasploitable2 |
| Bridged | Avoid for Metasploitable2 |

## Host-only Networking

Host-only networking creates a private network shared by the host and selected VMs. It does not connect the VMs directly to the external network. This is the safest choice for Metasploitable2 because the target is intentionally vulnerable.

Advantages:

- Isolates vulnerable services.
- Keeps traffic predictable.
- Allows easy IP verification.
- Supports repeatable screenshots and documentation.
- Reduces accidental scanning of external systems.

Common troubleshooting steps:

1. Confirm both VMs use the same host-only adapter.
2. Confirm both VMs are powered on.
3. Confirm both have addresses in the same subnet.
4. Ping the target from Kali.
5. Disable or review host firewall rules if host-to-guest testing fails.

## VirtualBox Networking

VirtualBox supports several network modes.

| Mode | Description | Best Use |
|---|---|---|
| NAT | VM accesses outside networks through VirtualBox NAT | Updates and internet access |
| NAT Network | Multiple VMs share a NAT-backed network | Small labs needing outbound access |
| Bridged Adapter | VM appears on physical network | Production-like testing with permission |
| Host-only Adapter | VM talks to host and other host-only VMs | Isolated security labs |
| Internal Network | VMs talk only to VMs on same internal network | Fully isolated multi-VM labs |

For Task 1, host-only is the main setting because it balances isolation with ease of use.

## Firewall

A firewall filters traffic based on rules. Firewalls can run on hosts, routers, cloud platforms, or inside applications. A firewall may block ping, TCP scans, or specific ports. Therefore, a failed scan does not always mean a host is offline.

Linux firewall tools include:

| Tool | Notes |
|---|---|
| iptables | Traditional Linux firewall interface |
| nftables | Modern Linux packet filtering framework |
| ufw | User-friendly firewall frontend |
| firewalld | Dynamic firewall manager |

In a lab, firewalls help teach how filtering changes scan results. In production, firewalls are essential but should not be the only control.

## VPN

A VPN creates an encrypted tunnel between networks or users. Security professionals use VPNs for remote access, lab platforms, and protected communication. A VPN can change routing, DNS, and visible source IPs, so it can affect scanning results.

For Task 1, a VPN is not required. The lab is local and isolated. In real work, always confirm authorization and scope before scanning through a VPN.

## Examples

### Check IP configuration

```bash
ip a
```

Identify the active host-only interface and confirm the IP address.

### Check routing

```bash
ip route
```

Verify that the lab subnet is directly connected.

### Ping target

```bash
ping -c 4 192.168.56.102
```

Successful replies mean basic network connectivity is working.

### Discover hosts in the lab subnet

```bash
nmap -sn 192.168.56.0/24
```

This identifies active hosts without performing a full port scan.

## Diagrams

### Host-only Lab

```text
+---------------- Host Machine ----------------+
|                                              |
|  VirtualBox Host-Only Adapter: 192.168.56.1  |
|             |                    |           |
|             |                    |           |
|   Kali Linux VM          Metasploitable2 VM  |
|   192.168.56.101         192.168.56.102      |
|   Scanner                Target              |
|                                              |
+----------------------------------------------+
```

### Packet Flow

```text
Kali terminal
    |
    | ping / nmap packet
    v
Kali host-only interface
    |
    v
VirtualBox host-only switch
    |
    v
Metasploitable2 host-only interface
    |
    v
Target service response
```

## Interview Questions

| Question | Answer Direction |
|---|---|
| What is the OSI model? | A seven-layer framework for understanding network communication. |
| What is the difference between TCP and UDP? | TCP is reliable and connection-oriented; UDP is connectionless and lower overhead. |
| What is a subnet mask? | It separates network bits from host bits in an IP address. |
| What does /24 mean? | The first 24 bits are the network portion. |
| Why use host-only networking? | It isolates lab machines while allowing local VM communication. |
| Why avoid bridged mode for Metasploitable2? | It can expose vulnerable services to the physical network. |
| What is DNS? | A system that maps names to IP addresses. |
| What is DHCP? | A protocol that automatically assigns network settings. |
| What is NAT? | Address translation between networks. |
| What is a port? | A logical endpoint for a service on a host. |
| Why might ping fail even if a host is online? | ICMP may be blocked by a firewall or disabled. |
| Why save Nmap output? | It preserves evidence and supports later analysis. |

Networking knowledge becomes powerful when combined with disciplined verification. In Task 1, the sequence is clear: configure the adapter, verify the IP address, test connectivity, scan the target, and document the results.


## Extended Networking Practice Notes

Networking becomes clearer when each troubleshooting step maps to a layer. If the VM is powered off, the problem is not DNS. If the IP address is missing, the problem is not an open port. If ping works but a TCP port is closed, the host is reachable but the service is not listening. This layered thinking is one of the most useful habits in cybersecurity.

### Layered Troubleshooting Method

| Step | Question | Command or Check |
|---|---|---|
| Physical or virtual link | Is the adapter connected? | VirtualBox network settings |
| Interface | Does the OS see the interface? | `ip link` |
| Addressing | Does the VM have the expected IP? | `ip a` |
| Local subnet | Are both systems in the same subnet? | Compare IP and prefix |
| Reachability | Can Kali reach the target? | `ping target` |
| Transport | Are ports open? | `nmap target` |
| Application | What service is behind the port? | `nmap -sV target` |

Following this order prevents wasted time. For example, running an aggressive Nmap scan before checking the IP address can produce misleading results.

### ARP and Local Delivery

On IPv4 local networks, ARP maps IP addresses to MAC addresses. When Kali wants to contact Metasploitable2 on the same subnet, it needs the target's MAC address. The system can display neighbors with:

```bash
ip neigh
```

If the neighbor table does not show the target after a ping attempt, local communication may not be working. In a VirtualBox host-only network, this usually points back to adapter selection, VM power state, or IP assignment.

### Broadcast Domains

A broadcast domain is the set of devices that receive broadcast traffic from each other. A host-only network creates a small virtual broadcast domain. This makes the lab predictable and keeps discovery traffic away from external networks. In a real enterprise, VLANs are used to split large networks into smaller broadcast domains for performance and security.

### Network Segmentation

Segmentation separates systems by role, trust level, or sensitivity. A vulnerable database server should not sit on the same unrestricted network as user workstations. A training target such as Metasploitable2 should not be bridged to a home or office LAN.

| Segment | Example Systems | Security Goal |
|---|---|---|
| User LAN | Workstations | Productivity with controlled access |
| Server LAN | Application servers | Protect business services |
| DMZ | Public web services | Isolate internet-facing systems |
| Management | Admin interfaces | Restrict privileged access |
| Lab | Training VMs | Contain vulnerable systems |

### TCP Handshake

TCP begins with a three-way handshake:

```text
Client -> SYN -> Server
Client <- SYN/ACK <- Server
Client -> ACK -> Server
```

Nmap uses TCP behavior to infer port states. If a port responds with SYN/ACK, it is likely open. If it responds with RST, it is closed. If there is no response or filtering behavior, it may be filtered.

### UDP Scanning Considerations

UDP does not use a handshake. This makes UDP scanning slower and less certain. A closed UDP port may return an ICMP unreachable message, while an open UDP service may not respond unless the probe matches the application protocol. Analysts should be careful when interpreting UDP results.

### DNS Security Notes

DNS is both essential and sensitive. Misconfigured DNS can leak internal naming, allow zone transfers, or support phishing through weak domain controls. In lab work, DNS may not be required, but in real reconnaissance it often reveals valuable infrastructure.

Useful DNS questions:

- Which domains belong to the organization?
- Which name servers are authoritative?
- Are mail exchange records exposed?
- Are subdomains discoverable?
- Are internal hostnames leaking?

### DHCP Troubleshooting

If a VM has no IP address, DHCP may have failed. Possible causes include disabled DHCP on the host-only adapter, wrong network mode, guest network service failure, or an interface that is administratively down.

```bash
ip a
sudo dhclient -v
```

Static addressing can make a lab easier to document, but DHCP is convenient for quick setup. Whichever method is used, record the final addresses before scanning.

### Firewall Interpretation

Firewalls can change what scanners see. An open service behind a firewall may appear filtered. A host that blocks ICMP may still have TCP services available. A mature analyst avoids overclaiming from one signal.

| Observation | Possible Meaning |
|---|---|
| Ping fails, TCP works | ICMP blocked |
| Port closed | Host reachable, service not listening |
| Port filtered | Firewall or packet filter likely |
| All ports filtered | Host firewall, network firewall, or wrong path |
| Service version hidden | Banner disabled or custom service |

### VirtualBox Mode Comparison for Security Labs

| Mode | Isolation | Internet Access | VM-to-VM Access | Recommended for Metasploitable2 |
|---|---|---|---|---|
| NAT | Medium | Yes | Limited by default | No |
| NAT Network | Medium | Yes | Yes | Only with care |
| Bridged | Low | Depends on LAN | Yes | No |
| Host-only | High | No by default | Yes | Yes |
| Internal Network | Very high | No | Yes, VMs only | Yes for advanced labs |

### Additional Interview Questions

| Question | Answer Direction |
|---|---|
| What is ARP? | IPv4 local mapping between IP addresses and MAC addresses. |
| What is a broadcast domain? | A group of devices that receive each other's broadcast traffic. |
| Why does TCP scanning differ from UDP scanning? | TCP has handshake behavior; UDP is connectionless. |
| What is segmentation? | Separating networks to limit access and reduce impact. |
| What does filtered mean in Nmap? | A firewall or filter prevents a clear open/closed decision. |
| Why can ping fail while HTTP works? | ICMP may be blocked while TCP port 80 is allowed. |
| What is the security value of a DMZ? | It isolates public-facing systems from internal networks. |
| Why document network diagrams? | Diagrams make scope, paths, and trust boundaries clear. |

### Practice Scenarios

1. Kali has `192.168.56.101/24` and Metasploitable2 has `192.168.57.102/24`. Explain why ping fails.
2. Ping fails, but `nmap -Pn target` finds open ports. Explain the likely reason.
3. A service is open on `127.0.0.1` only. Explain why another VM cannot reach it.
4. A target has port 80 open but port 443 closed. Explain what this says and what it does not say.
5. A host-only adapter has DHCP disabled. List two ways to solve address assignment.

Networking mastery is not memorizing every protocol number. It is the ability to reason from evidence: address, route, reachability, port state, service identity, and security impact.
