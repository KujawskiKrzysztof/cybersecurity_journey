# **Comprehensive Network Labs: Topology Setup & OSI/TCP-IP Analysis**

## **Lab Environment Overview**

### **Required Software & Resources**

| Component | Specification | Download Link |
|-----------|---------------|---------------|
| **Hypervisor** | VirtualBox 7.x or VMware Workstation | virtualbox.org |
| **Ubuntu Desktop** | 22.04 LTS or 24.04 LTS | ubuntu.com |
| **Windows** | Windows 10/11 (Evaluation) | microsoft.com/evalcenter |
| **Wireshark** | Latest version | wireshark.org |
| **Host RAM** | Minimum 8GB (16GB recommended) | - |
| **Host Storage** | 100GB free space | - |

### **VM Specifications**

| VM Name | OS | RAM | CPU | Storage | Network Adapters |
|---------|-----|-----|-----|---------|------------------|
| **Ubuntu-Server** | Ubuntu 22.04 | 2GB | 2 | 20GB | 2 NICs |
| **Ubuntu-Client1** | Ubuntu 22.04 | 2GB | 1 | 20GB | 1 NIC |
| **Ubuntu-Client2** | Ubuntu 22.04 | 2GB | 1 | 20GB | 1 NIC |
| **Windows-Client** | Windows 10/11 | 4GB | 2 | 40GB | 1 NIC |

---

## **Lab 0: Environment Setup**

### **Objective**
Set up the virtual lab environment with proper network configurations.

### **Step 1: Create Virtual Networks in VirtualBox**

```bash
# Open VirtualBox → File → Host Network Manager
# Create the following networks:

# Network 1: Management Network
Name: LabNet1
IPv4 Address: 192.168.10.1
IPv4 Network Mask: 255.255.255.0
DHCP Server: Disabled

# Network 2: Internal Network
Name: LabNet2
IPv4 Address: 192.168.20.1
IPv4 Network Mask: 255.255.255.0
DHCP Server: Disabled
```

### **Step 2: Create Ubuntu Server VM**

```bash
# VirtualBox Settings for Ubuntu-Server:

# System:
Base Memory: 2048 MB
Processors: 2

# Storage:
Create new VDI, 20GB, dynamically allocated

# Network:
Adapter 1: Host-Only Adapter → LabNet1
Adapter 2: Internal Network → intnet1
```

### **Step 3: Install Ubuntu Server/Desktop**

```bash
# During Ubuntu installation:
# 1. Choose "Minimal Installation" for faster setup
# 2. Create user: labuser / password: labpass123
# 3. Enable OpenSSH Server (if server edition)

# After installation, update system:
sudo apt update && sudo apt upgrade -y

# Install essential networking tools:
sudo apt install -y net-tools traceroute nmap tcpdump \
    wireshark iperf3 dnsutils curl wget openssh-server \
    apache2 isc-dhcp-server bind9 bridge-utils vlan
```

### **Step 4: Create Ubuntu Client VMs**

```bash
# Clone Ubuntu-Server or create new VMs
# VirtualBox Settings for Ubuntu-Client1 & Client2:

# Network:
Adapter 1: Internal Network → intnet1

# After boot, set static IP (Client1):
sudo nano /etc/netplan/00-installer-config.yaml
```

```yaml
# /etc/netplan/00-installer-config.yaml for Client1
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
        - 192.168.20.10/24
      routes:
        - to: default
          via: 192.168.20.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

```bash
# Apply network configuration
sudo netplan apply
```

### **Step 5: Create Windows Client VM**

```bash
# VirtualBox Settings for Windows-Client:

# System:
Base Memory: 4096 MB
Processors: 2
Enable EFI: Optional

# Network:
Adapter 1: Internal Network → intnet1

# After Windows installation:
# 1. Set static IP: 192.168.20.20/24
# 2. Gateway: 192.168.20.1
# 3. DNS: 8.8.8.8
```

### **Network Topology Diagram**

```
┌─────────────────────────────────────────────────────────────────┐
│                        HOST MACHINE                              │
│                    (VirtualBox Host)                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│    ┌──────────────────┐                                         │
│    │   LabNet1        │ ← Host-Only Network (192.168.10.0/24)   │
│    │  192.168.10.1    │                                         │
│    └────────┬─────────┘                                         │
│             │                                                    │
│             │ enp0s3 (192.168.10.10)                            │
│    ┌────────┴─────────┐                                         │
│    │  Ubuntu-Server   │ ← Acts as Router/Gateway                │
│    │   (Gateway)      │                                         │
│    └────────┬─────────┘                                         │
│             │ enp0s8 (192.168.20.1)                             │
│             │                                                    │
│    ┌────────┴─────────┐ ← Internal Network (192.168.20.0/24)    │
│    │     intnet1      │                                         │
│    └─┬───────┬───────┬┘                                         │
│      │       │       │                                          │
│      │       │       │                                          │
│  ┌───┴───┐ ┌─┴───┐ ┌─┴────────────┐                            │
│  │Client1│ │Client2│ │Windows-Client│                           │
│  │.20.10 │ │.20.11│ │   .20.20    │                            │
│  └───────┘ └──────┘ └─────────────┘                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## **Lab 1: Physical Layer Analysis (OSI Layer 1)**

### **Objective**
Understand physical layer concepts: link status, cable types, speed negotiation.

### **Duration**: 30 minutes

### **Part A: View Physical Interface Properties (Ubuntu)**

```bash
# Check all network interfaces
ip link show

# Expected output:
# 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
# 2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
# 3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
```

```bash
# View detailed interface statistics
ip -s link show enp0s3

# Check interface speed and duplex (requires ethtool)
sudo apt install ethtool
sudo ethtool enp0s3
```

**Expected Output Analysis:**
```
Settings for enp0s3:
    Supported ports: [ TP ]
    Supported link modes:   10baseT/Half 10baseT/Full 
                            100baseT/Half 100baseT/Full 
                            1000baseT/Full 
    Speed: 1000Mb/s          ← Layer 1: Physical speed
    Duplex: Full             ← Layer 1: Full duplex mode
    Link detected: yes       ← Layer 1: Cable connected
```

### **Part B: View Physical Interface Properties (Windows)**

```powershell
# Open PowerShell as Administrator

# List all network adapters
Get-NetAdapter

# View detailed adapter info
Get-NetAdapter | Format-List *

# Check link speed
Get-NetAdapter | Select-Object Name, Status, LinkSpeed

# View adapter hardware properties
Get-NetAdapterHardwareInfo
```

### **Part C: Simulate Cable Disconnect**

```bash
# On Ubuntu - Disable interface (simulates cable unplug)
sudo ip link set enp0s8 down

# Check status
ip link show enp0s8
# Notice: NO-CARRIER flag or state DOWN

# Re-enable interface
sudo ip link set enp0s8 up

# Monitor link state changes in real-time
sudo ip monitor link
```

### **Lab 1 Analysis Questions**

| Question | Your Answer | OSI Layer |
|----------|-------------|-----------|
| What speed is your virtual NIC running at? | | Layer 1 |
| Is the interface running half or full duplex? | | Layer 1 |
| What happens when you "disconnect" the cable? | | Layer 1 |
| What is the MTU of your interface? | | Layer 1/2 |

---

## **Lab 2: Data Link Layer Analysis (OSI Layer 2)**

### **Objective**
Analyze MAC addresses, Ethernet frames, ARP protocol, and switching behavior.

### **Duration**: 45 minutes

### **Part A: MAC Address Discovery**

**Ubuntu Commands:**
```bash
# View MAC addresses of all interfaces
ip link show

# Alternative method
cat /sys/class/net/enp0s3/address

# View detailed info with MAC
ifconfig -a

# View MAC address table (if acting as bridge)
brctl showmacs br0  # If bridge configured
```

**Windows Commands:**
```powershell
# View MAC addresses
Get-NetAdapter | Select-Object Name, MacAddress

# Alternative: Command Prompt
ipconfig /all

# View MAC in detailed format
getmac /v
```

### **Part B: ARP Protocol Analysis**

**On Ubuntu-Client1:**
```bash
# Clear ARP cache
sudo ip neigh flush all

# View current ARP table (empty after flush)
ip neigh show
# OR
arp -a

# Start Wireshark capture
sudo wireshark &
# Select enp0s3 interface, start capture

# Ping another machine to trigger ARP
ping -c 3 192.168.20.20  # Ping Windows client

# View updated ARP table
ip neigh show
```

**Expected ARP Table Output:**
```
192.168.20.20 dev enp0s3 lladdr 08:00:27:xx:xx:xx REACHABLE
192.168.20.1 dev enp0s3 lladdr 08:00:27:yy:yy:yy REACHABLE
```

### **Part C: Capture and Analyze Ethernet Frame**

```bash
# Using tcpdump to capture Layer 2 frames
sudo tcpdump -i enp0s3 -e -c 10

# -e : Print link-layer header (MAC addresses)
# -c 10 : Capture 10 packets

# Expected output:
# 08:00:27:aa:bb:cc > 08:00:27:dd:ee:ff, ethertype IPv4...
#     ↑ Source MAC        ↑ Dest MAC        ↑ EtherType
```

### **Part D: Wireshark Ethernet Frame Analysis**

```
1. Open Wireshark on Ubuntu-Client1
2. Start capture on enp0s3
3. Ping 192.168.20.20 from terminal
4. Stop capture
5. Click on an ICMP packet
6. Expand "Ethernet II" section
```

**Screenshot the following fields:**

| Field | Value | Layer |
|-------|-------|-------|
| Destination MAC | | Layer 2 |
| Source MAC | | Layer 2 |
| Type | 0x0800 (IPv4) | Layer 2 |
| Frame Length | | Layer 2 |

### **Part E: ARP Request/Reply Analysis**

**Wireshark Filter:**
```
arp
```

**Analyze ARP Packet Structure:**
```
┌────────────────────────────────────────────────────────┐
│                    ETHERNET HEADER                      │
├────────────────────────────────────────────────────────┤
│ Dest MAC: ff:ff:ff:ff:ff:ff (Broadcast)                │
│ Source MAC: 08:00:27:aa:bb:cc                          │
│ EtherType: 0x0806 (ARP)                                │
├────────────────────────────────────────────────────────┤
│                      ARP HEADER                         │
├────────────────────────────────────────────────────────┤
│ Hardware Type: Ethernet (1)                             │
│ Protocol Type: IPv4 (0x0800)                           │
│ Hardware Size: 6                                        │
│ Protocol Size: 4                                        │
│ Opcode: Request (1) / Reply (2)                        │
│ Sender MAC: 08:00:27:aa:bb:cc                          │
│ Sender IP: 192.168.20.10                               │
│ Target MAC: 00:00:00:00:00:00 (unknown)                │
│ Target IP: 192.168.20.20                               │
└────────────────────────────────────────────────────────┘
```

### **Lab 2 Exercises**

**Exercise 2.1: MAC Address Spoofing**
```bash
# On Ubuntu - Change MAC address temporarily
sudo ip link set enp0s3 down
sudo ip link set enp0s3 address 02:00:00:00:00:01
sudo ip link set enp0s3 up

# Verify change
ip link show enp0s3

# Test connectivity
ping 192.168.20.1

# Restore original (reboot or reconfigure)
```

**Exercise 2.2: ARP Cache Poisoning Demonstration**
```bash
# View current ARP entry for gateway
ip neigh show 192.168.20.1

# Manually add static ARP entry
sudo ip neigh add 192.168.20.1 lladdr 00:11:22:33:44:55 dev enp0s3

# Try to ping gateway - will fail!
ping 192.168.20.1

# Remove fake entry
sudo ip neigh del 192.168.20.1 dev enp0s3

# This demonstrates why ARP poisoning is dangerous
```

---

## **Lab 3: Network Layer Analysis (OSI Layer 3)**

### **Objective**
Analyze IP addressing, routing, ICMP, and packet structure.

### **Duration**: 60 minutes

### **Part A: IP Configuration Analysis**

**Ubuntu Commands:**
```bash
# View IP configuration
ip addr show

# View only IPv4 addresses
ip -4 addr show

# View routing table
ip route show
# OR
route -n
# OR
netstat -rn

# View default gateway
ip route | grep default
```

**Windows Commands:**
```powershell
# View IP configuration
ipconfig /all

# View routing table
route print

# PowerShell method
Get-NetIPConfiguration
Get-NetRoute
```

### **Part B: Configure Ubuntu-Server as Router**

**On Ubuntu-Server:**
```bash
# Enable IP forwarding
sudo sysctl -w net.ipv4.ip_forward=1

# Make permanent
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf

# Verify
cat /proc/sys/net/ipv4/ip_forward
# Output should be: 1

# Configure interfaces
sudo nano /etc/netplan/00-installer-config.yaml
```

```yaml
# Ubuntu-Server netplan configuration
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
        - 192.168.10.10/24
      routes:
        - to: default
          via: 192.168.10.1
    enp0s8:
      addresses:
        - 192.168.20.1/24
```

```bash
# Apply configuration
sudo netplan apply

# Verify both interfaces
ip addr show
```

### **Part C: ICMP Protocol Analysis**

**Start Wireshark, then ping:**
```bash
# On Ubuntu-Client1
ping -c 4 192.168.20.20

# Wireshark filter
icmp
```

**ICMP Packet Structure Analysis:**
```
┌──────────────────────────────────────────────────────────────┐
│                      ETHERNET HEADER (14 bytes)              │
├──────────────────────────────────────────────────────────────┤
│                      IP HEADER (20 bytes)                    │
│  ┌────────────────────────────────────────────────────────┐ │
│  │ Version: 4 (IPv4)                                      │ │
│  │ Header Length: 20 bytes                                │ │
│  │ Total Length: 84 bytes                                 │ │
│  │ TTL: 64                                                │ │
│  │ Protocol: 1 (ICMP)                                     │ │
│  │ Source IP: 192.168.20.10                               │ │
│  │ Destination IP: 192.168.20.20                          │ │
│  └────────────────────────────────────────────────────────┘ │
├──────────────────────────────────────────────────────────────┤
│                      ICMP HEADER (8 bytes)                   │
│  ┌────────────────────────────────────────────────────────┐ │
│  │ Type: 8 (Echo Request) or 0 (Echo Reply)               │ │
│  │ Code: 0                                                │ │
│  │ Checksum: 0xXXXX                                       │ │
│  │ Identifier: XXXX                                       │ │
│  │ Sequence Number: 1, 2, 3...                            │ │
│  └────────────────────────────────────────────────────────┘ │
├──────────────────────────────────────────────────────────────┤
│                      ICMP DATA (variable)                    │
└──────────────────────────────────────────────────────────────┘
```

### **Part D: Traceroute Analysis**

```bash
# On Ubuntu-Client1
traceroute 8.8.8.8

# Or with more details
traceroute -I 8.8.8.8  # Use ICMP instead of UDP

# Windows
tracert 8.8.8.8
```

**Wireshark Filter for Traceroute:**
```
icmp.type == 11 or icmp.type == 8 or icmp.type == 0
```

**Understanding Traceroute:**
```
Hop 1: TTL=1  → First router decrements to 0 → Sends ICMP Time Exceeded
Hop 2: TTL=2  → Second router decrements to 0 → Sends ICMP Time Exceeded
...
Final: TTL=n  → Destination reached → Sends ICMP Echo Reply
```

### **Part E: TTL Manipulation Experiment**

```bash
# Send ping with specific TTL
ping -c 1 -t 1 192.168.10.1    # TTL = 1
ping -c 1 -t 2 8.8.8.8         # TTL = 2 (won't reach Google)
ping -c 1 -t 64 8.8.8.8        # Normal TTL

# Observe different responses in Wireshark
```

### **Part F: Fragmentation Analysis**

```bash
# Send large packet that requires fragmentation
ping -c 1 -s 3000 192.168.20.20

# Wireshark filter
ip.flags.mf == 1 or ip.frag_offset > 0

# View fragmentation
# Packet 1: Flags = More Fragments, Offset = 0
# Packet 2: Flags = More Fragments, Offset = 1480
# Packet 3: Flags = 0, Offset = 2960
```

### **Lab 3 Exercises**

**Exercise 3.1: Create Multi-Hop Route**
```bash
# On Ubuntu-Server (Router)
# Add route to another network
sudo ip route add 10.0.0.0/24 via 192.168.10.1

# Verify
ip route show

# On clients, test routing
traceroute 10.0.0.1
```

**Exercise 3.2: Subnet Calculation Exercise**

Complete this table for network 192.168.20.0/24:

| Subnetting Task | Calculate | Answer |
|-----------------|-----------|--------|
| Network Address | | 192.168.20.0 |
| Broadcast Address | | |
| First Usable Host | | |
| Last Usable Host | | |
| Total Usable Hosts | | |
| Subnet Mask | | |

---

## **Lab 4: Transport Layer Analysis (OSI Layer 4)**

### **Objective**
Analyze TCP and UDP protocols, port numbers, and connection states.

### **Duration**: 75 minutes

### **Part A: TCP Three-Way Handshake**

**Setup Web Server on Ubuntu-Server:**
```bash
# Install and start Apache
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl status apache2

# Verify listening on port 80
sudo ss -tlnp | grep :80
```

**Capture TCP Handshake:**
```bash
# On Ubuntu-Client1
# Start Wireshark, then:

curl http://192.168.20.1

# Wireshark filter
tcp.port == 80
```

**TCP Three-Way Handshake Analysis:**
```
┌─────────────────────────────────────────────────────────────────┐
│                   TCP THREE-WAY HANDSHAKE                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Client (192.168.20.10)              Server (192.168.20.1)     │
│         │                                      │                 │
│         │──────── SYN (Seq=0) ────────────────>│  Step 1        │
│         │         Flags: SYN                   │                 │
│         │         Seq: 0                       │                 │
│         │                                      │                 │
│         │<─────── SYN-ACK (Seq=0, Ack=1) ─────│  Step 2        │
│         │         Flags: SYN, ACK              │                 │
│         │         Seq: 0, Ack: 1               │                 │
│         │                                      │                 │
│         │──────── ACK (Seq=1, Ack=1) ─────────>│  Step 3        │
│         │         Flags: ACK                   │                 │
│         │         Seq: 1, Ack: 1               │                 │
│         │                                      │                 │
│         │<═══════ DATA TRANSFER ══════════════>│  Connected!    │
│         │                                      │                 │
└─────────────────────────────────────────────────────────────────┘
```

**Document in Wireshark:**

| Packet # | Source | Destination | Flags | Seq | Ack | Description |
|----------|--------|-------------|-------|-----|-----|-------------|
| 1 | Client | Server | SYN | 0 | 0 | Connection Request |
| 2 | Server | Client | SYN,ACK | 0 | 1 | Connection Accepted |
| 3 | Client | Server | ACK | 1 | 1 | Handshake Complete |

### **Part B: TCP Connection States**

```bash
# On Ubuntu-Server, monitor TCP states
watch -n 1 'ss -tan'

# Generate traffic from client
curl http://192.168.20.1

# Observe states: LISTEN, SYN-RECV, ESTABLISHED, TIME-WAIT
```

**TCP State Diagram Exercise:**
```
┌──────────────────────────────────────────────────────────────────┐
│                     TCP STATE MACHINE                             │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│   CLOSED ──────────> LISTEN (Server waiting)                     │
│      │                   │                                        │
│      │ (Client)          │ SYN received                          │
│      ▼                   ▼                                        │
│   SYN-SENT ────────> SYN-RECEIVED                                │
│      │                   │                                        │
│      │ SYN+ACK           │ ACK received                          │
│      ▼                   ▼                                        │
│   ESTABLISHED <═══════════════════════> ESTABLISHED              │
│      │                                      │                     │
│      │ FIN sent                             │ FIN received       │
│      ▼                                      ▼                     │
│   FIN-WAIT-1                          CLOSE-WAIT                 │
│      │                                      │                     │
│      ▼                                      ▼                     │
│   FIN-WAIT-2                          LAST-ACK                   │
│      │                                      │                     │
│      ▼                                      │                     │
│   TIME-WAIT ─────────────────────────> CLOSED                    │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

### **Part C: TCP vs UDP Comparison**

**Setup UDP Service (DNS):**
```bash
# On Ubuntu-Server, install DNS server
sudo apt install bind9 -y
sudo systemctl start bind9

# Verify UDP port 53
sudo ss -ulnp | grep :53
```

**Generate UDP Traffic:**
```bash
# On Client - DNS query uses UDP
dig @192.168.20.1 google.com

# Wireshark filter
udp.port == 53
```

**Compare TCP and UDP Headers:**

| Field | TCP | UDP |
|-------|-----|-----|
| Source Port | ✓ | ✓ |
| Destination Port | ✓ | ✓ |
| Sequence Number | ✓ | ✗ |
| Acknowledgment | ✓ | ✗ |
| Flags (SYN, ACK, FIN) | ✓ | ✗ |
| Window Size | ✓ | ✗ |
| Checksum | ✓ | ✓ |
| Header Size | 20-60 bytes | 8 bytes |

### **Part D: Port Number Analysis**

```bash
# View all listening ports on Ubuntu
sudo ss -tulnp

# Common port categories:
# 0-1023: Well-known (system) ports
# 1024-49151: Registered ports
# 49152-65535: Dynamic/ephemeral ports

# View established connections
ss -tan state established
```

**Windows Port Analysis:**
```powershell
# View all listening ports
netstat -an | findstr LISTENING

# View with process names
netstat -anb

# PowerShell method
Get-NetTCPConnection -State Listen
```

### **Part E: TCP Flow Control - Window Size**

```bash
# Start large file transfer
# On Server, create test file
dd if=/dev/zero of=/var/www/html/largefile bs=1M count=100

# On Client, download while capturing
wget http://192.168.20.1/largefile

# Wireshark filter to see window size changes
tcp.analysis.window_update
```

**Observe:**
- Window size increases/decreases
- TCP flow control in action
- Slow start, congestion avoidance

### **Lab 4 Exercises**

**Exercise 4.1: Identify Common Ports**

Complete this table by capturing traffic:

| Service | Protocol | Port | Captured? |
|---------|----------|------|-----------|
| HTTP | TCP | 80 | |
| HTTPS | TCP | 443 | |
| SSH | TCP | 22 | |
| DNS | UDP | 53 | |
| DHCP Server | UDP | 67 | |
| DHCP Client | UDP | 68 | |

**Exercise 4.2: TCP Connection Termination**

```bash
# Capture a complete TCP session including termination
# Wireshark filter:
tcp.flags.fin == 1

# Document the 4-way terminationq:
# FIN →
# ACK ←
# FIN ←
# ACK →
```

---

## **Lab 5: Session, Presentation, and Application Layers (OSI Layers 5-7)**

### **Objective**
Analyze higher-layer protocols: HTTP, DNS, DHCP, SSH.

### **Duration**: 60 minutes

### **Part A: HTTP Protocol Analysis**

**Setup:**
```bash
# On Ubuntu-Server, create test webpage
echo "<html><body><h1>Lab Test Page</h1></body></html>" | \
  sudo tee /var/www/html/test.html
```

**Capture HTTP Request/Response:**
```bash
# On Client, start Wireshark then:
curl -v http://192.168.20.1/test.html

# Wireshark filter
http
```

**HTTP Request Analysis:**
```
┌─────────────────────────────────────────────────────────────────┐
│                        HTTP REQUEST                              │
├─────────────────────────────────────────────────────────────────┤
│ GET /test.html HTTP/1.1                    ← Request Line       │
│ Host: 192.168.20.1                         ← Headers            │
│ User-Agent: curl/7.81.0                                         │
│ Accept: */*                                                      │
│                                            ← Empty Line         │
│ [No Body for GET request]                  ← Body               │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                        HTTP RESPONSE                             │
├─────────────────────────────────────────────────────────────────┤
│ HTTP/1.1 200 OK                            ← Status Line        │
│ Date: Mon, 01 Jan 2024 12:00:00 GMT        ← Headers            │
│ Server: Apache/2.4.52 (Ubuntu)                                  │
│ Content-Length: 48                                               │
│ Content-Type: text/html                                          │
│                                            ← Empty Line         │
│ <html><body><h1>Lab Test Page</h1></body>  ← Body               │
│ </html>                                                          │
└─────────────────────────────────────────────────────────────────┘
```

### **Part B: DNS Protocol Analysis**

**Configure DNS Server (optional):**
```bash
# On Ubuntu-Server
sudo nano /etc/bind/named.conf.local
```

```
zone "lab.local" {
    type master;
    file "/etc/bind/db.lab.local";
};
```

```bash
# Create zone file
sudo cp /etc/bind/db.local /etc/bind/db.lab.local
sudo nano /etc/bind/db.lab.local
```

```
$TTL    604800
@       IN      SOA     ns.lab.local. admin.lab.local. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns.lab.local.
ns      IN      A       192.168.20.1
server  IN      A       192.168.20.1
client1 IN      A       192.168.20.10
client2 IN      A       192.168.20.11
win     IN      A       192.168.20.20
```

```bash
# Restart BIND
sudo systemctl restart bind9

# Test DNS
dig @192.168.20.1 server.lab.local
```

**DNS Packet Analysis:**
```
┌─────────────────────────────────────────────────────────────────┐
│                      DNS QUERY                                   │
├─────────────────────────────────────────────────────────────────┤
│ Transaction ID: 0x1234                                          │
│ Flags: Standard Query (0x0100)                                  │
│ Questions: 1                                                     │
│ Query: server.lab.local, Type A, Class IN                       │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                      DNS RESPONSE                                │
├─────────────────────────────────────────────────────────────────┤
│ Transaction ID: 0x1234                                          │
│ Flags: Standard Response (0x8180)                               │
│ Questions: 1                                                     │
│ Answers: 1                                                       │
│ Answer: server.lab.local, Type A, 192.168.20.1                  │
└─────────────────────────────────────────────────────────────────┘
```

### **Part C: DHCP Protocol Analysis**

**Configure DHCP Server:**
```bash
# On Ubuntu-Server
sudo nano /etc/dhcp/dhcpd.conf
```

```
default-lease-time 600;
max-lease-time 7200;
authoritative;

subnet 192.168.20.0 netmask 255.255.255.0 {
    range 192.168.20.100 192.168.20.200;
    option routers 192.168.20.1;
    option domain-name-servers 192.168.20.1, 8.8.8.8;
    option domain-name "lab.local";
}
```

```bash
# Specify interface
sudo nano /etc/default/isc-dhcp-server
# Add: INTERFACESv4="enp0s8"

# Start DHCP server
sudo systemctl restart isc-dhcp-server
```

**Capture DHCP Process (DORA):**
```bash
# On Client, reconfigure for DHCP
# Then release and renew

sudo dhclient -r enp0s3  # Release
sudo dhclient enp0s3     # Renew

# Wireshark filter
bootp or dhcp
```

**DHCP DORA Process:**
```
┌─────────────────────────────────────────────────────────────────┐
│                   DHCP DORA PROCESS                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Client                                    Server               │
│     │                                          │                 │
│     │─── (D)DISCOVER ─────────────────────────>│                │
│     │    Broadcast: "I need an IP!"            │                │
│     │                                          │                 │
│     │<── (O)FFER ─────────────────────────────│                │
│     │    "Here's 192.168.20.100"               │                │
│     │                                          │                 │
│     │─── (R)EQUEST ───────────────────────────>│                │
│     │    "I'll take 192.168.20.100"            │                │
│     │                                          │                 │
│     │<── (A)CK ───────────────────────────────│                │
│     │    "It's yours for 600 seconds"          │                │
│     │                                          │                 │
└─────────────────────────────────────────────────────────────────┘
```

### **Part D: SSH Analysis (Encrypted Session)**

```bash
# On Client, SSH to server
ssh labuser@192.168.20.1

# Wireshark filter
tcp.port == 22
```

**Observation:**
- TCP handshake visible (Layer 4)
- SSH version exchange visible (Layer 7)
- After key exchange, all data encrypted
- Cannot see actual commands/responses

**SSH Packet Types to Identify:**
1. Protocol version exchange
2. Key exchange init
3. Diffie-Hellman key exchange
4. Encrypted packets (application data)

---

## **Lab 6: Complete TCP/IP Stack Analysis**

### **Objective**
Trace a complete network transaction through all TCP/IP layers.

### **Duration**: 45 minutes

### **Scenario: Web Page Request**

**Complete a single HTTP request and analyze all layers:**

```bash
# On Client, clear caches first
sudo ip neigh flush all          # Clear ARP cache
sudo systemd-resolve --flush-caches  # Clear DNS cache (if using systemd-resolved)

# Start Wireshark capture

# Make HTTP request
curl http://server.lab.local/test.html

# Stop capture after request completes
```

### **Analysis Worksheet**

**Packet 1: DNS Query**

| Layer | TCP/IP Layer | OSI Layer(s) | What You See |
|-------|--------------|--------------|--------------|
| Application | Application | 7 | DNS Query for server.lab.local |
| Transport | Transport | 4 | UDP, Src Port: 54321, Dst Port: 53 |
| Internet | Internet | 3 | Src IP: 192.168.20.10, Dst IP: 192.168.20.1 |
| Link | Link | 2 | Src MAC: xx:xx, Dst MAC: yy:yy |
| Physical | Link | 1 | Frame on enp0s3 |

**Packet 2: DNS Response**
*(Fill in based on your capture)*

| Layer | TCP/IP Layer | What You See |
|-------|--------------|--------------|
| Application | | |
| Transport | | |
| Internet | | |
| Link | | |

**Packet 3-5: TCP Handshake**
*(Document SYN, SYN-ACK, ACK)*

**Packet 6: HTTP GET Request**

| Layer | Protocol | Key Fields |
|-------|----------|------------|
| Application | HTTP | GET /test.html HTTP/1.1 |
| Transport | TCP | Src Port: ?, Dst Port: 80 |
| Internet | IP | TTL: ?, Total Length: ? |
| Link | Ethernet | EtherType: 0x0800 |

**Packet 7: HTTP Response**
*(Document the response)*

### **Complete Protocol Stack Diagram**

```
┌─────────────────────────────────────────────────────────────────┐
│         APPLICATION DATA: "<html>...</html>"                    │
├─────────────────────────────────────────────────────────────────┤
│                     ↓ Encapsulation                             │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ HTTP Header + Data                                          ││
│  │ "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n..."        ││
│  └─────────────────────────────────────────────────────────────┘│
│                     ↓ Add TCP Header                            │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┬───────────────────────────────────────────────┐│
│  │ TCP Header  │ HTTP Header + Data                            ││
│  │ Src:80      │                                               ││
│  │ Dst:54321   │                                               ││
│  │ Seq/Ack     │                                               ││
│  └─────────────┴───────────────────────────────────────────────┘│
│                     ↓ Add IP Header                             │
├─────────────────────────────────────────────────────────────────┤
│  ┌───────────┬─────────────┬───────────────────────────────────┐│
│  │ IP Header │ TCP Header  │ HTTP Header + Data                ││
│  │ Src IP    │             │                                   ││
│  │ Dst IP    │             │                                   ││
│  │ TTL: 64   │             │                                   ││
│  └───────────┴─────────────┴───────────────────────────────────┘│
│                     ↓ Add Ethernet Header + Trailer             │
├─────────────────────────────────────────────────────────────────┤
│┌────────────┬───────────┬─────────────┬──────────────┬─────────┐│
││ Eth Header │ IP Header │ TCP Header  │ HTTP + Data  │ Eth FCS ││
││ Dst MAC    │           │             │              │ CRC     ││
││ Src MAC    │           │             │              │         ││
││ Type:0x800 │           │             │              │         ││
│└────────────┴───────────┴─────────────┴──────────────┴─────────┘│
│                                                                  │
│                     ↓ Transmit as Bits                          │
├─────────────────────────────────────────────────────────────────┤
│  01101001 10110100 00111010 11100101 ... (electrical signals)   │
└─────────────────────────────────────────────────────────────────┘
```

---

## **Lab 7: Cross-Platform Communication Lab**

### **Objective**
Test and analyze communication between Ubuntu and Windows systems.

### **Duration**: 45 minutes

### **Part A: File Sharing (SMB)**

**Setup Samba on Ubuntu-Server:**
```bash
# Install Samba
sudo apt install samba -y

# Create shared directory
sudo mkdir -p /srv/share
sudo chmod 777 /srv/share
echo "Hello from Ubuntu" | sudo tee /srv/share/test.txt

# Configure Samba
sudo nano /etc/samba/smb.conf
```

Add at the end:
```
[labshare]
    path = /srv/share
    browseable = yes
    read only = no
    guest ok = yes
```

```bash
# Restart Samba
sudo systemctl restart smbd

# Verify share
smbclient -L //localhost -N
```

**Access from Windows:**
```powershell
# Map network drive
net use Z: \\192.168.20.1\labshare

# Or in File Explorer: \\192.168.20.1\labshare

# Capture SMB traffic in Wireshark
# Filter: smb or smb2
```

### **Part B: Remote Desktop (RDP) Analysis**

**On Windows, enable RDP:**
```
Settings → System → Remote Desktop → Enable
```

**From Ubuntu, connect to Windows:**
```bash
# Install RDP client
sudo apt install remmina -y

# Connect to Windows
remmina &
# Add new connection: RDP, 192.168.20.20

# Capture traffic
# Wireshark filter: tcp.port == 3389
```

### **Part C: Cross-Platform Ping Comparison**

```bash
# Ubuntu to Windows
ping -c 5 192.168.20.20

# Windows to Ubuntu (different default settings)
ping 192.168.20.10
```

**Compare:**
| Aspect | Ubuntu Ping | Windows Ping |
|--------|-------------|--------------|
| Default Count | Infinite (-c to limit) | 4 packets |
| Default TTL | 64 | 128 |
| Packet Size | 64 bytes | 32 bytes |

---

## **Lab 8: Troubleshooting Scenarios**

### **Objective**
Apply OSI model knowledge to diagnose network issues.

### **Scenario 1: No Connectivity**

**Setup the problem:**
```bash
# On Ubuntu-Client1
sudo ip link set enp0s3 down  # Disable interface
```

**Troubleshoot layer by layer:**

| Layer | Check | Command | Expected if Problem |
|-------|-------|---------|---------------------|
| 1 (Physical) | Link status | `ip link show` | "state DOWN" |
| 2 (Data Link) | MAC address | `ip link show` | Missing or invalid |
| 3 (Network) | IP address | `ip addr show` | No IP assigned |
| 3 (Network) | Gateway | `ip route` | No default route |
| 4 (Transport) | Port open | `ss -tulnp` | Service not listening |
| 7 (Application) | Service running | `systemctl status apache2` | Service stopped |

**Fix:**
```bash
sudo ip link set enp0s3 up
```

### **Scenario 2: Can Ping IP but Not Hostname**

**Setup:**
```bash
# Break DNS
sudo mv /etc/resolv.conf /etc/resolv.conf.bak
```

**Troubleshoot:**
```bash
ping 192.168.20.1        # Works (Layer 3 OK)
ping server.lab.local    # Fails (Layer 7 - DNS issue)

# Fix:
sudo mv /etc/resolv.conf.bak /etc/resolv.conf
```

### **Scenario 3: Slow Network Performance**

**Use iperf3 to test bandwidth:**
```bash
# On Server
iperf3 -s

# On Client
iperf3 -c 192.168.20.1

# Analyze results
# Normal: ~900 Mbps for Gigabit
# Problem: < 100 Mbps might indicate duplex mismatch
```

---

## **Lab Summary & Review**

### **OSI Layer Quick Reference**

| Layer | Name | Protocols Analyzed | Tools Used |
|-------|------|-------------------|------------|
| 7 | Application | HTTP, DNS, DHCP, SSH | curl, dig, Wireshark |
| 6 | Presentation | SSL/TLS | Wireshark (encrypted) |
| 5 | Session | SMB, RPC | smbclient, Wireshark |
| 4 | Transport | TCP, UDP | ss, netstat, Wireshark |
| 3 | Network | IP, ICMP, ARP | ping, traceroute, ip |
| 2 | Data Link | Ethernet | ip link, ethtool |
| 1 | Physical | - | ethtool, cable test |

### **TCP/IP Layer Quick Reference**

| TCP/IP Layer | Equivalent OSI | What You Analyzed |
|--------------|----------------|-------------------|
| Application | 5, 6, 7 | HTTP, DNS, DHCP |
| Transport | 4 | TCP handshake, UDP |
| Internet | 3 | IP routing, ICMP ping |
| Link | 1, 2 | MAC addresses, ARP |

### **Wireshark Filters Cheat Sheet**

| Purpose | Filter |
|---------|--------|
| ARP only | `arp` |
| ICMP only | `icmp` |
| TCP only | `tcp` |
| UDP only | `udp` |
| Specific host | `ip.addr == 192.168.20.10` |
| HTTP traffic | `http` |
| DNS traffic | `dns` |
| TCP handshake | `tcp.flags.syn == 1` |
| Specific port | `tcp.port == 80` |

### **Key Commands Summary**

**Ubuntu:**
```bash
ip addr show          # View IP configuration
ip link show          # View interface status
ip route show         # View routing table
ip neigh show         # View ARP table
ss -tulnp            # View listening ports
ping, traceroute     # Connectivity tests
tcpdump, wireshark   # Packet capture
```

**Windows:**
```powershell
ipconfig /all        # View IP configuration
Get-NetAdapter       # View interface status
route print          # View routing table
arp -a               # View ARP table
netstat -an          # View connections
ping, tracert        # Connectivity tests
```

---

## **Lab Completion Checklist**

| Lab | Topic | Completed | Notes |
|-----|-------|-----------|-------|
| 0 | Environment Setup | ☐ | |
| 1 | Physical Layer | ☐ | |
| 2 | Data Link Layer | ☐ | |
| 3 | Network Layer | ☐ | |
| 4 | Transport Layer | ☐ | |
| 5 | Upper Layers | ☐ | |
| 6 | TCP/IP Stack | ☐ | |
| 7 | Cross-Platform | ☐ | |
| 8 | Troubleshooting | ☐ | |

**Congratulations!** Upon completing these labs, you will have hands-on experience with all OSI and TCP/IP layers, packet analysis, and network troubleshooting!


