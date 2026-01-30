
## VMware Workstation - 7 Virtual Machines Setup

---

## Network Architecture Diagram (ASCII)

```
┌─────────────────────────────────────────────────────────────────────┐
│               EXTERNAL/INTERNET ZONE (VMnet8 - NAT)                 │
│                        192.168.x.x/24                               │
│  ┌───────────────────────────────────────────────────────────┐     │
│  │          ⚔️  Kali Linux (Attacker/Testing)                │     │
│  │              IP: DHCP (192.168.x.x)                       │     │
│  │              Role: Penetration Testing                    │     │
│  └───────────────────────────────────────────────────────────┘     │
└─────────────────────────────────┬───────────────────────────────────┘
                                  │ NAT
                                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  🛡️  pfSense FIREWALL/ROUTER                        │
│                     (Main Security Gateway)                         │
│  ┌───────────────────────────────────────────────────────────┐     │
│  │  Interface Configuration:                                 │     │
│  │  • WAN:        VMnet8 (NAT) - Internet                    │     │
│  │  • DMZ:        VMnet2 - 10.10.10.1                        │     │
│  │  • Corporate:  VMnet3 - 10.10.20.1                        │     │
│  │  • Database:   VMnet4 - 10.10.30.1                        │     │
│  │  • Management: VMnet5 - 10.10.40.1                        │     │
│  │                                                            │     │
│  │  Resources: 1-2 vCPU, 1GB RAM, 8GB Disk                   │     │
│  └───────────────────────────────────────────────────────────┘     │
└───────┬──────────────┬──────────────┬─────────────┬────────────────┘
        │              │              │             │
    DMZ │          Corporate      Database     Management
        │              │              │             │
        ▼              ▼              ▼             ▼
┌───────────────┐ ┌──────────────────────┐ ┌──────────────┐ ┌──────────────┐
│   DMZ ZONE    │ │  CORPORATE INTERNAL  │ │   DATABASE   │ │  MANAGEMENT  │
│   (VMnet2)    │ │      (VMnet3)        │ │   (VMnet4)   │ │   (VMnet5)   │
│ 10.10.10.0/24 │ │   10.10.20.0/24      │ │10.10.30.0/24 │ │10.10.40.0/24 │
│               │ │                      │ │              │ │              │
│ ┌───────────┐ │ │ ┌────────────────┐   │ │ ┌──────────┐ │ │ ┌──────────┐ │
│ │🌐 Web     │ │ │ │🖥️  Windows    │  │ │ │🗄️  MySQL  │ │ │ │💻 Linux  │ │
│ │  Server   │ │ │ │   Server 2022  │  │ │ │  Database│ │ │ │   Admin  │ │
│ │           │ │ │ │                │  │ │ │  Server  │ │ │ │          │ │
│ │Ubuntu/    │ │ │ │DC, DNS, Files  │  │ │ │          │ │ │ │Ubuntu/   │ │
│ │CentOS     │ │ │ │                │  │ │ │PostgreSQL│ │ │ │Kali      │ │
│ │           │ │ │ │10.10.20.10     │  │ │ │          │ │ │ │          │ │
│ │10.10.10.10│ │ │ │                │  │ │ │10.10.30.10│ │ │10.10.40.10│ │
│ │           │ │ │ │2vCPU, 4GB RAM  │  │ │ │          │ │ │          │ │
│ │1vCPU      │ │ │ │60GB Disk       │  │ │ │1vCPU     │ │ │1-2vCPU   │ │
│ │2GB RAM    │ │ │ └────────────────┘  │ │ │2GB RAM   │ │ │2GB RAM   │ │
│ │20GB Disk  │ │ │                      │ │ │20GB Disk │ │ │20GB Disk │ │
│ └───────────┘ │ │ ┌────────────────┐  │ │ └──────────┘ │ │ └──────────┘ │
│               │ │ │💻 Windows 10/11│  │ │              │ │              │
│Public Access  │ │ │   Client       │  │ │Backend Only  │ │Admin Access  │
│HTTP/HTTPS     │ │ │                │  │ │Restricted    │ │to All Zones  │
│               │ │ │DHCP Client     │  │ │              │ │              │
│               │ │ │                │  │ │              │ │Security Tools│
│               │ │ │2vCPU, 4GB RAM  │  │ │              │ │              │
│               │ │ │40GB Disk       │  │ │              │ │              │
│               │ │ └────────────────┘  │ │              │ │              │
└───────────────┘ └─────────────────────┘ └──────────────┘ └──────────────┘
```

---

## Virtual Machine Specifications

### 1. pfSense Firewall/Router

- **Role:** Central firewall and routing gateway
- **Resources:** 1-2 vCPU, 1GB RAM, 8GB Disk
- **Network Interfaces:** 5 adapters
    - Adapter 1: VMnet8 (NAT/WAN)
    - Adapter 2: VMnet2 (DMZ)
    - Adapter 3: VMnet3 (Corporate)
    - Adapter 4: VMnet4 (Database)
    - Adapter 5: VMnet5 (Management)
- **IP Addresses:**
    - WAN: DHCP from VMnet8
    - DMZ: 10.10.10.1
    - Corporate: 10.10.20.1
    - Database: 10.10.30.1
    - Management: 10.10.40.1
- **OS:** pfSense (free download from pfsense.org)

---

### 2. Web Server (DMZ)

- **Role:** Public-facing web application server
- **Resources:** 1 vCPU, 2GB RAM, 20GB Disk
- **Network:** VMnet2 (DMZ)
- **IP Address:** 10.10.10.10
- **OS:** Ubuntu Server 22.04 LTS or CentOS Stream 9
- **Services:** Apache/Nginx, PHP, public website
- **Security:** Only HTTP/HTTPS accessible from external network

---

### 3. Windows Server 2022

- **Role:** Domain Controller, DNS, File Server
- **Resources:** 2 vCPU, 4GB RAM, 60GB Disk
- **Network:** VMnet3 (Corporate)
- **IP Address:** 10.10.20.10
- **OS:** Windows Server 2022 Evaluation (180-day trial)
- **Services:**
    - Active Directory Domain Services
    - DNS Server
    - DHCP Server (optional)
    - File Sharing

---

### 4. Database Server (Secure Zone)

- **Role:** Backend database for applications
- **Resources:** 1 vCPU, 2GB RAM, 20GB Disk
- **Network:** VMnet4 (Database/Secure)
- **IP Address:** 10.10.30.10
- **OS:** Ubuntu Server 22.04 LTS
- **Services:** MySQL 8.0 or PostgreSQL 14
- **Security:** No direct internet access, restricted port access

---

### 5. Windows 10/11 Client

- **Role:** Standard corporate workstation
- **Resources:** 2 vCPU, 4GB RAM, 40GB Disk
- **Network:** VMnet3 (Corporate)
- **IP Address:** DHCP from pfSense (10.10.20.x)
- **OS:** Windows 10/11 Evaluation
- **Purpose:** Simulate end-user workstation

---

### 6. Linux Admin Workstation

- **Role:** IT administration and security analysis
- **Resources:** 1-2 vCPU, 2GB RAM, 20GB Disk
- **Network:** VMnet5 (Management)
- **IP Address:** 10.10.40.10
- **OS:** Ubuntu Desktop 22.04 or Kali Linux
- **Tools:**
    - Wireshark (packet analysis)
    - Nmap (network scanning)
    - SSH clients
    - Security monitoring tools

---

### 7. Kali Linux (Attacker Machine)

- **Role:** External threat simulation and penetration testing
- **Resources:** 2 vCPU, 2-4GB RAM, 30GB Disk
- **Network:** VMnet8 (External/NAT)
- **IP Address:** DHCP from VMnet8 (192.168.x.x)
- **OS:** Kali Linux (latest version)
- **Purpose:**
    - Penetration testing
    - Security assessment
    - Attack simulation

---

## Network Segmentation Details

### Segment 1: External/Internet (VMnet8 - NAT)

- **Purpose:** Simulates the public internet
- **IP Range:** 192.168.x.x/24 (assigned by VMware NAT)
- **Virtual Machines:** Kali Linux (attacker)
- **Access:** Limited ingress, unrestricted egress

### Segment 2: DMZ - Demilitarized Zone (VMnet2)

- **Purpose:** Hosts public-facing services
- **IP Range:** 10.10.10.0/24
- **Gateway:** 10.10.10.1 (pfSense)
- **Virtual Machines:** Web Server
- **Firewall Rules:**
    - ✅ Inbound: HTTP (80), HTTPS (443) from External
    - ✅ Outbound: Limited to specific services
    - ❌ No access to internal networks (Corporate, Database)

### Segment 3: Corporate Internal (VMnet3)

- **Purpose:** Internal business network
- **IP Range:** 10.10.20.0/24
- **Gateway:** 10.10.20.1 (pfSense)
- **Virtual Machines:** Windows Server, Windows Client
- **Firewall Rules:**
    - ✅ Access to DMZ web services
    - ✅ Access to Database (specific ports only)
    - ✅ Full internet access via pfSense NAT
    - ❌ No inbound from DMZ
    - ❌ No inbound from External

### Segment 4: Database/Secure (VMnet4)

- **Purpose:** Backend data storage (highly restricted)
- **IP Range:** 10.10.30.0/24
- **Gateway:** 10.10.30.1 (pfSense)
- **Virtual Machines:** Database Server
- **Firewall Rules:**
    - ✅ Accept connections from Corporate on ports 3306 (MySQL) or 5432 (PostgreSQL)
    - ✅ Accept connections from Management segment
    - ❌ No direct internet access
    - ❌ No access from DMZ
    - ❌ No outbound to Corporate (one-way only)

### Segment 5: Management (VMnet5)

- **Purpose:** IT administration and monitoring
- **IP Range:** 10.10.40.0/24
- **Gateway:** 10.10.40.1 (pfSense)
- **Virtual Machines:** Linux Admin Workstation
- **Firewall Rules:**
    - ✅ Full access to all internal segments (for administration)
    - ✅ SSH, RDP, management protocols allowed
    - ✅ Full internet access

---

## Firewall Rules Summary (pfSense)

### WAN → DMZ Rules

```
Protocol: TCP
Ports: 80, 443
Destination: 10.10.10.10 (Web Server)
Action: ALLOW

All other traffic
Action: DENY
```

### WAN → Corporate/Database/Management Rules

```
All traffic
Action: DENY (default deny)
```

### Corporate → DMZ Rules

```
Protocol: TCP
Ports: 80, 443
Destination: 10.10.10.0/24
Action: ALLOW
```

### Corporate → Database Rules

```
Protocol: TCP
Ports: 3306 (MySQL) or 5432 (PostgreSQL)
Source: 10.10.20.0/24
Destination: 10.10.30.10
Action: ALLOW

All other traffic
Action: DENY
```

### DMZ → Corporate Rules

```
All traffic
Action: DENY
```

### DMZ → Database Rules

```
All traffic
Action: DENY
```

### Management → All Rules

```
All traffic to internal segments
Action: ALLOW
```

---

## VMware Workstation Network Configuration

### Step-by-Step Virtual Network Editor Setup

1. **Open Virtual Network Editor:**
    
    - VMware Workstation → Edit → Virtual Network Editor
    - Click "Change Settings" (requires admin rights)
2. **Create Custom Networks:**
    
    **VMnet2 (DMZ):**
    
    - Click "Add Network" → Select VMnet2
    - Type: Host-only
    - Subnet: 10.10.10.0
    - Subnet Mask: 255.255.255.0
    - Disable "Use local DHCP service"
    - Disable "Connect host virtual adapter"
    
    **VMnet3 (Corporate):**
    
    - Click "Add Network" → Select VMnet3
    - Type: Host-only
    - Subnet: 10.10.20.0
    - Subnet Mask: 255.255.255.0
    - Disable "Use local DHCP service"
    - Disable "Connect host virtual adapter"
    
    **VMnet4 (Database):**
    
    - Click "Add Network" → Select VMnet4
    - Type: Host-only
    - Subnet: 10.10.30.0
    - Subnet Mask: 255.255.255.0
    - Disable "Use local DHCP service"
    - Disable "Connect host virtual adapter"
    
    **VMnet5 (Management):**
    
    - Click "Add Network" → Select VMnet5
    - Type: Host-only
    - Subnet: 10.10.40.0
    - Subnet Mask: 255.255.255.0
    - Disable "Use local DHCP service"
    - Disable "Connect host virtual adapter"
    
    **VMnet8 (External/NAT):**
    
    - Already configured by default
    - Type: NAT
    - Keep existing configuration
3. **Click "Apply" and "OK"**
    

---

## Installation Order

1. **pfSense** (first - acts as gateway for all segments)
2. **Windows Server** (provides AD/DNS for domain)
3. **Database Server** (backend services)
4. **Web Server** (DMZ service)
5. **Windows Client** (join to domain)
6. **Linux Admin** (management tools)
7. **Kali Linux** (testing/attacks)

---

## Learning Exercises

### Exercise 1: Basic Connectivity Testing

- Verify each VM can ping its default gateway
- Test connectivity between same-segment VMs
- Verify isolation between different segments

### Exercise 2: Firewall Rule Creation

- Create pfSense rules to allow web traffic
- Block all other traffic by default
- Test rules with browser access

### Exercise 3: DMZ Security

- Access web server from Kali Linux (external)
- Attempt to access web server from Corporate network
- Try to access Corporate network FROM web server (should fail)

### Exercise 4: Database Access Control

- Configure application to connect from Corporate to Database
- Verify web server CANNOT access database directly
- Test that database cannot initiate connections to Corporate

### Exercise 5: Attack Simulation

- Use Nmap from Kali to scan DMZ
- Attempt SQL injection or web attacks
- Monitor with Wireshark from Management segment

### Exercise 6: VPN Configuration

- Set up OpenVPN on pfSense
- Connect remotely to Management network
- Test administrative access to all segments

### Exercise 7: IDS/IPS Implementation

- Enable Snort or Suricata on pfSense
- Generate test traffic
- Analyze detected threats

### Exercise 8: Traffic Analysis

- Capture packets between segments
- Identify protocol usage
- Detect anomalous traffic patterns

---

## Security Best Practices Demonstrated

✅ **Network Segmentation:** Isolate critical assets ✅ **DMZ Architecture:** Separate public services from internal networks ✅ **Least Privilege:** Grant minimum necessary access ✅ **Defense in Depth:** Multiple security layers ✅ **Monitoring:** Management segment for security oversight ✅ **Controlled Access:** Firewall rules for every segment ✅ **One-way Communication:** Database accepts but doesn't initiate

---

## Troubleshooting Tips

### VMs Can't Get IP Addresses

- Verify pfSense DHCP is enabled for each interface
- Check VM network adapter is connected to correct VMnet
- Restart network service or reboot VM

### Can't Access Web Server from External

- Check pfSense WAN rules allow port 80/443
- Verify port forwarding if using NAT
- Confirm web server service is running

### Segments Can Communicate When They Shouldn't

- Review pfSense firewall rules
- Check rule order (more specific rules first)
- Verify interface assignments are correct

### pfSense Can't Access Internet

- Check VMnet8 NAT configuration
- Verify WAN interface received IP from DHCP
- Test DNS resolution

---

## Download Links

- **pfSense:** https://www.pfsense.org/download/
- **Ubuntu Server:** https://ubuntu.com/download/server
- **Windows Server 2022 Eval:** https://www.microsoft.com/en-us/evalcenter/
- **Windows 10/11 Eval:** https://www.microsoft.com/en-us/evalcenter/
- **Kali Linux:** https://www.kali.org/get-kali/
- **CentOS Stream:** https://www.centos.org/download/

---

## Resource Requirements

**Minimum Host System:**

- CPU: 4 cores (6+ recommended)
- RAM: 16GB (24GB+ recommended)
- Disk: 300GB free space
- VMware Workstation Pro or Player

**Running All 7 VMs Simultaneously:**

- Total RAM: ~15-17GB
- Total Disk: ~250GB
- Total vCPUs: 11-13

**Tip:** You don't need to run all VMs at once. Run specific combinations for each exercise.

---

## Document Version

Version: 1.0 Created: January 2026 Purpose: Network Segmentation Lab Training Platform: VMware Workstation Total VMs: 7