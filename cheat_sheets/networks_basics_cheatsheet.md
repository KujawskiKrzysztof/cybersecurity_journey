# Comprehensive Learning Path for Computer Networks

**Target Duration: 8-10 Weeks**

This path is structured to build your knowledge incrementally, from core fundamentals to designing a small segmented LAN. Each phase includes conceptual learning, practical examples, and hands-on practice to reinforce understanding.

## Prerequisites

Only basic computer literacy is required: familiarity with operating systems (Windows/Linux), file management, and basic command-line usage.

---

## Phase 1: Foundational Network Concepts (1 Week)

Start with the absolute basics to contextualize all subsequent topics:

| Topic | Key Learning Objectives |
|-------|--------------------------|
| What is a Computer Network? | Define networks, their core goals (resource sharing, communication, service access), and key performance metrics (bandwidth, latency, packet loss) |
| Network Types by Scale | Differentiate LAN (Local Area Network), WAN (Wide Area Network), MAN (Metropolitan Area Network), and PAN (Personal Area Network) |
| Core Network Components | Understand the role of NICs, switches, routers, hubs, access points (APs), firewalls, and common cabling (UTP Cat 5e/6, fiber optic, coax) |
| Network Topologies | Compare star, bus, ring, mesh, and hybrid topologies; learn why star is the dominant topology for modern LANs |

---

## Phase 2: OSI/ISO and TCP/IP Reference Models (2 Weeks)

These models are the framework for understanding how data travels across networks. Below is a detailed breakdown of each layer for both models:

### 1. OSI 7-Layer Model

The OSI model is a theoretical standard for teaching and troubleshooting network communication. Each layer handles specific tasks and interacts only with the layers immediately above and below it:

| Layer # | Layer Name | Core Functions | Key Protocols/Standards | Data Unit | Example Devices/Components |
|---------|------------|----------------|--------------------------|-----------|-----------------------------|
| 7 | Application | Provides user-facing network services; interacts with end-user applications | HTTP/HTTPS, FTP, SMTP, DNS, DHCP, SSH, POP3 | Data | Web browsers, email clients |
| 6 | Presentation | Translates data to a format readable by the application layer; handles encryption, compression, and formatting | SSL/TLS, JPEG, MP3, ASCII | Data | OS protocol stacks |
| 5 | Session | Manages connections between devices; establishes, maintains, and terminates sessions | NetBIOS, RPC | Data | OS protocol stacks |
| 4 | Transport | End-to-end data delivery; ensures reliable transmission (TCP) or low-latency connectionless transmission (UDP); uses port numbers | TCP, UDP, SCTP | Segment (TCP) / Datagram (UDP) | Layer 4 switches |
| 3 | Network | Logical addressing; routing data between different networks; path selection | IP (v4/v6), ICMP, ARP, RIP, OSPF | Packet | Routers, Layer 3 switches |
| 2 | Data Link | Frames data for physical transmission; error detection; MAC addressing; access to physical media (split into LLC and MAC sublayers) | Ethernet (802.3), PPP, HDLC, STP, VLANs | Frame | Switches, bridges |
| 1 | Physical | Transmits raw binary data over physical media; defines voltage levels, cable types, and connectors | IEEE 802.3 (Physical Layer), RS-232, Fibre Channel | Bit | NICs, hubs, cable modems |

### 2. TCP/IP 4-Layer Model

The TCP/IP model is the practical framework used for all real-world networks (derived from the US DoD model). It maps closely to the OSI model:

| Layer Name | Corresponding OSI Layers | Core Functions | Key Protocols |
|------------|---------------------------|----------------|---------------|
| Application | 5, 6, 7 | Combines session, presentation, and application layer functions | HTTP, DNS, DHCP, SSH |
| Transport | 4 | Same as OSI Layer 4 | TCP, UDP |
| Internet | 3 | Same as OSI Layer 3 | IP, ICMP, ARP |
| Link | 1, 2 | Combines physical and data link layer functions | Ethernet, PPP |

**Critical Note:** OSI is a theoretical standard for teaching and troubleshooting. TCP/IP is the actual protocol suite used in all modern networks.

---

## Phase 3: Core Network Protocols (2 Weeks)

Deep dive into the most critical protocols, grouped by their layer in the TCP/IP model:

| Layer | Key Protocols to Learn | Critical Concepts |
|-------|-------------------------|-------------------|
| Link | Ethernet (802.3) | MAC addresses, frame structure, CSMA/CD collision avoidance |
| | STP (Spanning Tree Protocol) | Prevents looped networks in switched environments |
| Internet | IP (v4/v6) | Logical addressing, packet routing |
| | ICMP | Error reporting and diagnostics (ping, traceroute) |
| | ARP | Maps IP addresses to MAC addresses |
| Transport | TCP | 3-way handshake (SYN → SYN-ACK → ACK), reliable ordered delivery, flow/congestion control |
| | UDP | Connectionless delivery, low latency for real-time apps (VoIP, video streaming) |
| | Port Numbers | Well-known (0-1023), registered (1024-49151), ephemeral (49152-65535) |
| Application | DHCP | Automates IP address assignment via the DORA process (Discover, Offer, Request, ACK) |
| | DNS | Translates domain names to IP addresses |
| | HTTP/HTTPS | Web communication; TLS encryption for HTTPS |
| | SSH | Secure remote access to network devices |

---

## Phase 4: IPv4 and IPv6 Basics (2 Weeks)

IP addressing is the backbone of network communication. This phase covers both versions in detail:

### 4.1 IPv4 Fundamentals

| Topic | Key Learning Objectives |
|-------|--------------------------|
| Address Structure | 32-bit dotted-decimal notation (e.g., 192.168.1.1); distinguish network vs host address portions |
| Address Classes | Class A, B, C (unicast), D (multicast), E (reserved) |
| CIDR Notation | Subnetting with CIDR (e.g., 192.168.1.0/24) to create smaller, more efficient networks |
| Subnetting Basics | Calculate subnet masks, network addresses, broadcast addresses, and usable host ranges |
| DHCP | Understand the DORA process for automatic IP assignment |
| NAT | Purpose of Network Address Translation; types (Static NAT, Dynamic NAT, PAT) |

### 4.2 IPv6 Fundamentals

| Topic | Key Learning Objectives |
|-------|--------------------------|
| Motivation for IPv6 | Address exhaustion in IPv4; simplified header, built-in IPsec security, and auto-configuration |
| Address Structure | 128-bit hexadecimal notation (e.g., 2001:db8:0:1::1); rules for compressed notation |
| Address Types | Unicast (Global, Unique Local, Link-Local), Multicast, Anycast |
| Subnetting | Simplified subnetting (typically /64 prefixes for end-user networks) |
| Transition Mechanisms | Dual Stack, NAT64, and tunneling (6to4, Teredo) for coexistence with IPv4 |

---

## Phase 5: Designing a Small Segmented LAN (1-2 Weeks)

Segmentation improves LAN performance, security, and manageability by reducing broadcast domains. Below is a step-by-step guide to designing a small office LAN:

### 5.1 What is LAN Segmentation?

Core benefits:
- Reduces broadcast traffic (each segment is its own broadcast domain)
- Isolates traffic between departments for security
- Improves performance by limiting traffic to relevant segments

### 5.2 Common Segmentation Methods

| Method | How It Works | Use Case |
|--------|--------------|----------|
| Subnetting | Split a single IP network into smaller logical networks using subnet masks | Basic segmentation for small networks |
| VLANs | Virtual Local Area Networks: Split a physical switch into multiple logical switches | Segmentation for departments on the same physical switch |
| Layer 3 Switching | Route traffic between VLANs/subnets without a dedicated router | Scalable segmentation for medium-small LANs |

### 5.3 Step-by-Step Small LAN Design Example

**Scenario:** 50-user office with 3 departments: Admin (15 users), Sales (25 users), IT (10 users)

| Step | Action | Details |
|------|--------|---------|
| 1 | Requirements Gathering | All users need internet access; IT traffic must be isolated; shared printer for Admin/Sales |
| 2 | IP Address Plan | Use private IPv4 range 192.168.10.0/24. Subnet as follows:<br>- Admin: 192.168.10.0/27 (30 usable hosts)<br>- Sales: 192.168.10.32/26 (62 usable hosts)<br>- IT: 192.168.10.96/28 (14 usable hosts) |
| 3 | VLAN Design | Assign VLAN IDs: VLAN 10 (Admin), VLAN 20 (Sales), VLAN 30 (IT). Configure access ports on switches for each VLAN |
| 4 | Device Selection | 2 access switches (24-port each), 1 core Layer 3 switch (for inter-VLAN routing), 1 firewall/router for internet access |
| 5 | Routing & Security | Configure inter-VLAN routing on the core switch; block non-IT access to the IT subnet via ACLs; enable DHCP for each VLAN |
| 6 | Testing | Verify connectivity with ping; test inter-VLAN routing; validate traffic isolation |

---

## Phase 6: Hands-On Practice (Ongoing)

Networking is a practical skill. Use these tools to reinforce your learning:

1. **Cisco Packet Tracer**: Free simulator for designing and testing LANs; ideal for practicing VLANs, subnetting, and routing.
2. **Wireshark**: Packet capture tool to analyze real network traffic (e.g., observe TCP 3-way handshakes or DNS queries).
3. **Command Line Tools**: Use `ipconfig`/`ifconfig`, `ping`, `traceroute`, and `nslookup` to troubleshoot basic network issues.
4. **GNS3**: Advanced simulator that uses real router/switch IOS images for realistic practice.

---

## Recommended Resources

| Type | Resource | Notes |
|------|----------|-------|
| Book | Computer Networking: A Top-Down Approach (Kurose & Ross) | Definitive textbook for network fundamentals |
| Book | CCNA Official Cert Guide (Wendell Odom) | Excellent for practical LAN design and routing concepts |
| Course | Cisco Networking Academy: CCNA 1 & 2 | Free, structured courses covering all core topics |
| Course | Coursera: Computer Networks (Georgia Tech) | University-level course for deep conceptual understanding |
| YouTube | Professor Messer | Free, concise videos for network fundamentals |

---

## Next Steps After This Path

- Practice designing larger LANs with multiple sites
- Learn basic WAN technologies (MPLS, VPNs)
- Explore network security fundamentals (firewalls, IDS/IPS)
- Prepare for entry-level certifications (CCNA, CompTIA Network+)