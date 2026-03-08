# 🌐 Enterprise Network Infrastructure Build

[![Network Status](https://img.shields.io/badge/Network-Operational-success)](https://github.com)
[![Devices](https://img.shields.io/badge/Devices-10%2F10-blue)](https://github.com)
[![Connectivity](https://img.shields.io/badge/Internet-100%25-brightgreen)](https://github.com)
[![Security](https://img.shields.io/badge/Security-Multi--Layer-orange)](https://github.com)

> A complete enterprise network infrastructure build from scratch, demonstrating multi-zone architecture, defense-in-depth security, and full internet connectivity across all network segments.

## 📋 Table of Contents

- [Overview](#overview)
- [Network Architecture](#network-architecture)
- [What Was Built](#what-was-built)
- [Network Zones](#network-zones)
- [Device Configuration](#device-configuration)
- [Configuration Journey](#configuration-journey)
- [Key Challenges Solved](#key-challenges-solved)
- [Technical Documentation](#technical-documentation)
- [Lessons Learned](#lessons-learned)
- [Author](#author)

## 🎯 Overview

This project documents the complete build process of an enterprise-grade network infrastructure from scratch. Built over one week in a Proxmox virtual environment, this implementation demonstrates practical networking skills including:

- **Network Segmentation** - 5 distinct security zones
- **Multi-Layer Routing** - Complex routing with transit networks
- **Firewall Configuration** - OPNsense with zone-based security
- **NAT Implementation** - Multi-layer Network Address Translation
- **Troubleshooting** - Systematic problem-solving approach

**Project Duration:** 1 Week  
**Final Status:** ✅ **OPERATIONAL** - All devices online with full internet connectivity

## 🏗️ Network Architecture

### High-Level Design

```
Internet (10.10.X.X)
    ↓
Router-WAN (VyOS)
    ↓ 192.168.2.X
Firewall (OPNsense) - 3 Interfaces
    ↓
    ├─→ Transit Network (10.0.2.X)
    │       ↓
    │   Router-LAN (VyOS)
    │       ↓
    │   Client LAN (192.168.1.X)
    │       └─ Clients & Servers
    │
    └─→ Screened Subnet (10.0.1.X)
            └─ Public-Facing Servers
```

### Architecture Highlights

- **5 Network Zones** with distinct security levels
- **10 Configured Devices** with internet connectivity
- **Multi-Layer NAT** for address translation
- **Firewall-Based Segmentation** using OPNsense
- **Transit Network Design** to eliminate subnet conflicts

## ✨ What Was Built

### Network Infrastructure
✅ **Router-WAN** - VyOS router providing internet gateway  
✅ **Firewall** - OPNsense with 3 interfaces for zone segmentation  
✅ **Router-LAN** - VyOS router for internal network routing  
✅ **Security Desktop** - Kali Linux for monitoring  

### Client Devices
✅ **Windows 11 Client** - Workstation with internet access  
✅ **RHEL Client** - Linux workstation with internet access  
✅ **Windows Server** - Ready for future services (AD/DNS)  

### Server Infrastructure (Screened Subnet)
✅ **WebServer** - Windows server in DMZ  
✅ **Database Server** - RHEL server in DMZ  

### Networking Features
✅ **Multi-zone segmentation** (WAN, Transit, LAN, DMZ)  
✅ **Multi-layer routing** with static routes  
✅ **NAT configuration** at multiple layers  
✅ **Firewall rules** for zone-based access  
✅ **100% internet connectivity** across all zones  

## 🌍 Network Zones

| Zone | Network | Purpose | Gateway | Status |
|------|---------|---------|---------|--------|
| **WAN/Internet** | 10.10.X.X | Internet Connection | 10.10.0.1 | ✅ Online |
| **WAN Switch** | 192.168.2.X | Security/Monitoring Zone | 192.168.2.1 | ✅ Online |
| **Transit** | 10.0.2.X | Firewall ↔ Router-LAN | 10.0.2.2 | ✅ Online |
| **Client LAN** | 192.168.1.X | Internal Trusted Network | 192.168.1.254 | ✅ Online |
| **Screened Subnet** | 10.0.1.X | DMZ for Public Services | 10.0.1.1 | ✅ Online |

## 💻 Device Configuration

### Infrastructure Devices

| Device | IP Address(es) | OS | Role |
|--------|----------------|-----|------|
| **Router-WAN** | 10.10.83.205, 192.168.2.1 | VyOS | Internet Gateway |
| **Firewall** | 192.168.2.2, 10.0.2.2, 10.0.1.1 | OPNsense | Central Firewall |
| **Router-LAN** | 10.0.2.1, 192.168.1.254 | VyOS | Internal Gateway |
| **Security Desktop** | 192.168.2.50 | Kali Linux | Monitoring |

### Client & Server Devices

| Device | IP Address | OS | Network Zone |
|--------|-----------|-----|--------------|
| **Windows Client** | 192.168.1.100 | Windows 11 | Client LAN |
| **RHEL Client** | 192.168.1.101 | RHEL | Client LAN |
| **Server** | 192.168.1.5 | Windows Server 2025 | Client LAN |
| **WebServer** | 10.0.1.100 | Windows Server | Screened Subnet |
| **Database** | 10.0.1.200 | RHEL | Screened Subnet |

**All devices configured with full internet connectivity**

## 📖 Configuration Journey

### Phase 1: Router-WAN Setup
**Challenge:** Lab environment used different IP scheme than documentation  
**Solution:** Discovered actual addressing (10.10.X.X) through interface inspection  
**Result:** Internet connectivity established with proper routing

**Configuration:**
- WAN interface: 10.10.83.205 (DHCP)
- LAN interface: 192.168.2.1/24
- Static routes to internal networks
- NAT rules for all zones

---

### Phase 2: Firewall Configuration
**Challenge:** Interface assignments not persisting, virtual environment detection issues  
**Solution:** Used console menu Option 2 instead of Option 1 for persistence  
**Result:** Three-interface firewall operational

**Configuration:**
- WAN (vtnet0): 192.168.2.2/24
- Transit (vtnet2): 10.0.2.2/24
- Screened Subnet (vtnet1): 10.0.1.1/24

---

### Phase 3: Router-LAN Implementation
**Challenge:** Subnet conflict - both interfaces on 192.168.1.X  
**Solution:** Created dedicated transit network (10.0.2.X)  
**Result:** Clean routing with no ambiguity

**Configuration:**
- Transit interface: 10.0.2.1/24
- Client interface: 192.168.1.254/24
- NAT for client traffic
- Default route via firewall

---

### Phase 4: Client Configuration
**Challenge:** Clients could ping gateway but not internet  
**Solution:** Missing NAT configuration on Router-LAN  
**Result:** Full internet connectivity achieved

**Configurations:**
- Windows 11: Static IP with proper gateway
- RHEL: Static IP via nmcli/GUI
- Windows Server: Static IP configuration

---

### Phase 5: Screened Subnet Deployment
**Challenge:** Firewall blocking all traffic by default  
**Solution:** Created OPT1 firewall rule allowing internet access  
**Result:** DMZ servers online with controlled access

**Configuration:**
- Firewall rule: OPT1 net → any (allow)
- Both servers configured with 10.0.1.1 gateway
- Internet connectivity verified

---

### Phase 6: Firewall GUI Access
**Challenge:** "Block private networks" feature preventing WAN access  
**Solution:** Disabled RFC1918 blocking on WAN interface (lab only)  
**Result:** Secure GUI access from Security Desktop

## 🔧 Key Challenges Solved

### 1. Documentation vs. Reality
**Problem:** Requirements specified 172.31.0.X network that didn't exist in lab  
**Solution:** Systematic investigation to discover actual 10.10.X.X scheme  
**Learning:** Always verify actual environment before trusting documentation

### 2. Virtual Environment Limitations
**Problem:** Proxmox bridge assignments locked, interface detection unreliable  
**Solution:** Adapted configuration to work within constraints  
**Learning:** Virtual environments have different characteristics than physical

### 3. Subnet Conflict
**Problem:** Router-LAN with both interfaces on same subnet (192.168.1.X)  
**Solution:** Created transit network (10.0.2.X) for firewall connection  
**Learning:** Proper IP space allocation prevents routing ambiguity

### 4. Missing NAT Configuration
**Problem:** Clients reached gateway but couldn't access internet  
**Solution:** Configured masquerade NAT on Router-LAN outbound interface  
**Learning:** Multi-layer NAT essential for private network internet access

### 5. Firewall Default-Deny
**Problem:** Screened Subnet devices blocked from internet  
**Solution:** Created explicit allow rules via OPNsense GUI  
**Learning:** Default security behaviors are features, not bugs

### 6. Bridge Connectivity Issues
**Problem:** Some Proxmox bridges appeared non-functional  
**Solution:** Worked around by using working bridges, adapted design  
**Learning:** Infrastructure constraints require creative solutions

### 7. Configuration Persistence
**Problem:** OPNsense interface IPs not persisting after reload  
**Solution:** Used correct console menu option that writes to config file  
**Learning:** Different configuration methods have different persistence behavior

## 📚 Technical Documentation

### Complete Documentation Set

This repository includes:

- **[Comprehensive Build Report](docs/Enterprise_Network_Build_Report.docx)** - 25+ page detailed report covering every phase
- **[Network Topology](docs/network_topology.md)** - Text-based topology with all IP addresses and configurations
- **[Visual Network Diagram](diagrams/network_topology_diagram.html)** - Interactive HTML diagram with color-coding
- **[Quick Reference Guide](docs/QUICK_REFERENCE.md)** - IP addresses, commands, and troubleshooting

### Traffic Flow Example

**Client (192.168.1.100) → Internet:**

```
1. Client sends packet → Gateway 192.168.1.254 (Router-LAN eth4)
2. Router-LAN NAT: 192.168.1.100 → 10.0.2.1
3. Router-LAN forwards → 10.0.2.2 (Firewall transit)
4. Firewall routes internally → WAN interface
5. Firewall forwards → 192.168.2.1 (Router-WAN)
6. Router-WAN NAT: 192.168.2.2 → 10.10.83.205
7. Router-WAN forwards → 10.10.0.1 (Internet gateway)
8. Packet reaches Internet ✅
```

**Return traffic follows reverse path using NAT state tables**

## 💡 Lessons Learned

### Technical Insights

**1. Layer 2 vs. Layer 3 Troubleshooting**
- Use ARP tables to distinguish physical vs. logical connectivity issues
- "Host unreachable" + ARP "(incomplete)" = Layer 2 problem
- Check both physical connectivity AND IP configuration

**2. Documentation Verification**
- Lab environments frequently differ from documentation
- Verify through multiple methods before trusting specs
- Use diagnostic commands to discover actual configuration

**3. Network Segmentation Planning**
- Proper IP address allocation prevents routing conflicts
- Transit networks essential for multi-layer routing
- Each subnet should have single, clear purpose

**4. NAT Configuration**
- Multi-layer NAT required for nested private networks
- Without NAT, upstream devices can't route replies
- Symptoms appear as one-way connectivity

**5. Default Security Behaviors**
- Firewalls blocking traffic is correct behavior
- Understand security features before disabling
- Default-deny policies protect networks

**6. Virtual Infrastructure Constraints**
- Interface link detection may be unreliable
- Bridge assignments may be locked
- Configuration persistence methods differ from physical

### Process Improvements

**1. Systematic Troubleshooting**
- Work outside-in: Internet → Router-WAN → Firewall → Router-LAN → Clients
- Verify each layer before proceeding
- Document each step for reference

**2. Iterative Problem-Solving**
- First approach may not work
- Multiple attempts with different methods often needed
- Persistence and adaptability essential

**3. Configuration Documentation**
- Record every command and configuration change
- Note both successes and failures
- Detailed notes invaluable for troubleshooting

## 🛠️ Technologies Used

### Networking Platforms
- **VyOS** - Enterprise routing (Router-WAN, Router-LAN)
- **OPNsense** - Enterprise firewall with web GUI
- **Proxmox** - Virtualization infrastructure

### Operating Systems
- **Windows Server 2025** - Server platform
- **Windows 11** - Client workstation
- **RHEL (Red Hat Enterprise Linux)** - Linux clients and servers
- **Kali Linux** - Security monitoring

### Networking Concepts
- Static routing
- Network Address Translation (NAT)
- Firewall rule management
- Zone-based security
- Defense-in-depth architecture
- Bridge/VLAN segmentation

## 📊 Project Metrics

| Metric | Value |
|--------|-------|
| **Build Duration** | 1 Week |
| **Devices Configured** | 10 (100%) |
| **Network Zones** | 5 |
| **Internet Connectivity** | 100% |
| **Major Challenges Resolved** | 7+ |
| **Routers Configured** | 2 (VyOS) |
| **Firewalls Configured** | 1 (OPNsense) |
| **Documentation Pages** | 25+ |

## 👨‍💻 Author

**Justin**  
Network Engineer  

*Demonstrated Skills:*
- Enterprise network design and implementation
- Multi-zone security architecture
- VyOS routing configuration
- OPNsense firewall management
- Network troubleshooting and problem-solving
- Technical documentation

---

## 🎓 Project Purpose

This project demonstrates:
- Practical enterprise networking skills
- Systematic troubleshooting methodology
- Ability to adapt to infrastructure constraints
- Professional documentation practices
- Defense-in-depth security implementation

---

**Network Status:** ✅ **FULLY OPERATIONAL**

*All devices configured • All zones online • Full internet connectivity • Multi-layer security*

---

*Last Updated: February 19, 2026*
