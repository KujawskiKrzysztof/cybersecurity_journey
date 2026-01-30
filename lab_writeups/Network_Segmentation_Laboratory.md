I'll help you design a comprehensive network segmentation lab using VMware Workstation. This setup will give you hands-on experience with VLANs, firewalls, DMZ zones, and inter-segment routing.
## Network Segmentation Lab Architecture

### Virtual Machines (7 Total)

**1. pfSense Firewall/Router** (Main Gateway)

- Role: Central firewall and router
- vCPU: 1-2, RAM: 1GB, Disk: 8GB
- Network Adapters: 4-5 (one per segment)
- OS: pfSense (free)

**2. Windows Server 2022** (Corporate Server)

- Role: Domain Controller, DNS, File Server
- vCPU: 2, RAM: 4GB, Disk: 60GB
- Network: Internal Corporate segment
- OS: Windows Server 2022 Evaluation

**3. Web Server (DMZ)**

- Role: Public-facing web server
- vCPU: 1, RAM: 2GB, Disk: 20GB
- Network: DMZ segment
- OS: Ubuntu Server or CentOS

**4. Database Server (Secure Zone)**

- Role: Backend database
- vCPU: 1, RAM: 2GB, Disk: 20GB
- Network: Secure/Database segment
- OS: Ubuntu Server with MySQL/PostgreSQL

**5. Windows 10/11 Client** (Corporate Workstation)

- Role: Regular user workstation
- vCPU: 2, RAM: 4GB, Disk: 40GB
- Network: Internal Corporate segment
- OS: Windows 10/11 Evaluation

**6. Linux Client** (IT/Admin Workstation)

- Role: Administrative/security workstation
- vCPU: 1-2, RAM: 2GB, Disk: 20GB
- Network: Management segment
- OS: Ubuntu Desktop or Kali Linux

**7. Attacker Machine** (Optional: External Threat Simulation)

- Role: Security testing and penetration testing
- vCPU: 2, RAM: 2-4GB, Disk: 30GB
- Network: External/Internet segment
- OS: Kali Linux

---

## Network Segment Design

### Segment Layout:

1. **External/Internet** - 192.168.1.0/24 (NAT to host)
2. **DMZ** - 10.10.10.0/24
3. **Corporate Internal** - 10.10.20.0/24
4. **Database/Secure** - 10.10.30.0/24
5. **Management** - 10.10.40.0/24

---

## VMware Workstation Configuration Steps

### Step 1: Create Virtual Network Adapters

1. Open VMware Workstation → **Edit** → **Virtual Network Editor**
2. Click **Add Network** and create:
    - **VMnet2** - Host-only (DMZ) - 10.10.10.0/24
    - **VMnet3** - Host-only (Corporate) - 10.10.20.0/24
    - **VMnet4** - Host-only (Database) - 10.10.30.0/24
    - **VMnet5** - Host-only (Management) - 10.10.40.0/24
3. Disable DHCP on all custom networks (pfSense will handle this)
4. Keep **VMnet8** (NAT) for external connectivity

### Step 2: pfSense Router Setup

**Network Adapters:**

- Adapter 1: NAT (VMnet8) - WAN interface
- Adapter 2: VMnet2 - DMZ
- Adapter 3: VMnet3 - Corporate
- Adapter 4: VMnet4 - Database
- Adapter 5: VMnet5 - Management

**Configuration:**

1. Install pfSense
2. Assign interfaces during setup
3. Configure DHCP servers for each internal network
4. Set up firewall rules:
    - **WAN → DMZ**: Block all except HTTP/HTTPS to web server
    - **Corporate → DMZ**: Allow HTTP/HTTPS
    - **Corporate → Database**: Allow only specific ports (3306, 5432)
    - **Database → Corporate**: Block (one-way communication)
    - **Management → All**: Allow (for administration)
    - **DMZ → Corporate/Database**: Block

### Step 3: Virtual Machine Network Assignment

| VM              | Network Adapter      | Segment         |
| --------------- | -------------------- | --------------- |
| pfSense         | Multiple (see above) | Gateway         |
| Windows Server  | VMnet3               | Corporate       |
| Web Server      | VMnet2               | DMZ             |
| Database Server | VMnet4               | Database/Secure |
| Windows Client  | VMnet3               | Corporate       |
| Linux Admin     | VMnet5               | Management      |
| Kali Linux      | VMnet8               | External        |

### Step 4: IP Address Scheme

```
pfSense Interfaces:
- WAN: DHCP (from VMnet8)
- DMZ: 10.10.10.1
- Corporate: 10.10.20.1
- Database: 10.10.30.1
- Management: 10.10.40.1

Servers/Clients:
- Web Server: 10.10.10.10
- Windows Server: 10.10.20.10
- Database: 10.10.30.10
- Windows Client: DHCP (10.10.20.x)
- Linux Admin: 10.10.40.10
- Kali: DHCP (192.168.x.x)
```

---

## Learning Exercises

1. **Basic Segmentation**: Verify isolation between segments using ping
2. **Firewall Rules**: Create rules to allow specific traffic between segments
3. **DMZ Testing**: Access web server from external network, block from corporate
4. **Database Security**: Configure application-level access only
5. **Port Scanning**: Use Kali to scan from external network
6. **VPN Setup**: Configure VPN on pfSense for remote management
7. **IDS/IPS**: Enable Snort/Suricata on pfSense
8. **Traffic Analysis**: Capture packets between segments

---

## Alternative Lightweight Option (if resources are limited)

If 7 VMs are too resource-intensive, you can reduce to 5:

- Combine Windows Server and Database Server
- Use one client VM (Windows or Linux)
- Skip the dedicated attacker machine (use host OS with tools)

[<iframe src="https://claude.site/public/artifacts/7d724ca5-1233-4f30-b79c-31f9a5a4e649/embed" title="Claude Artifact" width="100%" height="600" frameborder="0" allow="clipboard-write" allowfullscreen></iframe>](https://claude.ai/public/artifacts/7d724ca5-1233-4f30-b79c-31f9a5a4e649)