# 🌐 Enterprise Network Infrastructure Project

[![Network Status](https://img.shields.io/badge/Network-Operational-success)](https://github.com)
[![Devices](https://img.shields.io/badge/Devices-10%2F10-blue)](https://github.com)
[![Zones](https://img.shields.io/badge/Zones-5-brightgreen)](https://github.com)
[![Security](https://img.shields.io/badge/Security-Multi--Layer-orange)](https://github.com)

> A comprehensive enterprise network infrastructure build demonstrating multi-zone architecture, defense-in-depth security, and full internet connectivity across all network segments.

## 📋 Table of Contents

- [Overview](#overview)
- [Network Architecture](#network-architecture)
- [Key Features](#key-features)
- [Network Zones](#network-zones)
- [Device Inventory](#device-inventory)
- [Configuration Journey](#configuration-journey)
- [Documentation](#documentation)
- [Technologies Used](#technologies-used)
- [Traffic Flow](#traffic-flow)
- [Lessons Learned](#lessons-learned)
- [Future Enhancements](#future-enhancements)
- [Author](#author)

## 🎯 Overview

This project documents the complete build process of an enterprise-grade network infrastructure from scratch. Built over the course of one week, this implementation showcases practical application of enterprise networking principles including:

- **Network Segmentation** - 5 distinct security zones
- **Multi-Layer Routing** - Complex routing with transit networks
- **Defense-in-Depth** - Firewall with proper zone isolation
- **NAT Configuration** - Multi-layer Network Address Translation
- **Production-Ready** - All devices configured with internet connectivity

**Project Status:** ✅ **OPERATIONAL** - All zones online with full internet access

## 🏗️ Network Architecture

### High-Level Design

```
Internet (10.10.X.X)
    ↓
Router-WAN (VyOS)
    ↓
Firewall (OPNsense) - 3 Interfaces
    ↓
    ├─→ Router-LAN (Transit Network: 10.0.2.X)
    │       ↓
    │   Client LAN (192.168.1.X)
    │       ├─ Windows 11 Client
    │       ├─ RHEL Client
    │       └─ AD/DNS Server
    │
    └─→ Screened Subnet (10.0.1.X)
            ├─ WebServer
            └─ Database Server
```

### Architecture Highlights

- **5 Network Zones** with distinct security levels
- **10 Configured Devices** across infrastructure and client layers
- **Multi-Layer NAT** (Router-LAN + Router-WAN)
- **Firewall-Based Segmentation** using OPNsense
- **Transit Network Design** to avoid subnet conflicts

## ✨ Key Features

### Security
- ✅ Three-tier security architecture (WAN, DMZ, LAN)
- ✅ Firewall with default-deny policy
- ✅ Zone-based access control
- ✅ NAT hiding internal addressing
- ✅ Screened subnet for public services

### Network Design
- ✅ Proper IP address space allocation
- ✅ Transit networks between routing layers
- ✅ Multi-layer routing configuration
- ✅ Full internet connectivity for all zones
- ✅ Scalable architecture for future growth

### Infrastructure
- ✅ Mixed OS environment (Windows, Linux)
- ✅ Enterprise-grade routing (VyOS)
- ✅ Professional firewall (OPNsense)
- ✅ Virtualized infrastructure (Proxmox)
- ✅ Ready for Active Directory integration

## 🌍 Network Zones

| Zone | Network | Bridge | Purpose | Devices |
|------|---------|--------|---------|---------|
| **WAN/Internet** | 10.10.X.X | maindhcp | Internet Connection | Router-WAN |
| **WAN Switch** | 192.168.2.X | nct17a1 | Security/Monitoring Zone | Router-WAN, Firewall, Security Desktop |
| **Transit** | 10.0.2.X | nct17a3 | Firewall ↔ Router-LAN | Firewall, Router-LAN |
| **Client LAN** | 192.168.1.X | nct17a4 | Internal Trusted Network | Router-LAN, Clients, AD/DNS |
| **Screened Subnet** | 10.0.1.X | nct17a2 | DMZ for Public Services | Firewall, WebServer, Database |

## 💻 Device Inventory

### Infrastructure Layer

| Device | VM | IP Address(es) | OS | Role |
|--------|-----|----------------|-----|------|
| **Router-WAN** | 677 | 10.10.83.205, 192.168.2.1 | VyOS | Internet Gateway |
| **Firewall** | 675 | 192.168.2.2, 10.0.2.2, 10.0.1.1 | OPNsense | Central Firewall |
| **Router-LAN** | 676 | 10.0.2.1, 192.168.1.254 | VyOS | Internal Gateway |
| **Security Desktop** | 674 | 192.168.2.50 | Kali Linux | Security Monitoring |

### Client Layer

| Device | VM | IP Address | OS | Role |
|--------|-----|------------|-----|------|
| **AD/DNS Server** | 691 | 192.168.1.5 | Windows Server 2025 | Domain Services |
| **Windows Client** | 697 | 192.168.1.100 | Windows 11 | Workstation |
| **RHEL Client** | 678 | 192.168.1.101 | RHEL | Workstation |

### Server Layer (Screened Subnet)

| Device | VM | IP Address | OS | Role |
|--------|-----|------------|-----|------|
| **WebServer** | 699 | 10.0.1.100 | Windows Server | Public Web Services |
| **Database** | 680 | 10.0.1.200 | RHEL | Database Server |

**Total:** 10 devices configured with 100% internet connectivity

## 📖 Configuration Journey

### Major Phases

1. **Router-WAN Configuration**
   - Interface discovery and mapping
   - Internet connectivity establishment
   - Static routing implementation
   - NAT configuration

2. **Firewall Deployment**
   - Three-interface configuration
   - Interface assignment persistence issues resolved
   - Default security behavior understanding
   - Web GUI access configuration

3. **Network Topology Discovery**
   - Security Desktop placement decision
   - Architectural gap resolution
   - Zone assignment finalization

4. **Router-LAN Implementation**
   - Subnet conflict identification
   - Transit network creation (10.0.2.X)
   - NAT configuration for client network
   - Internet connectivity breakthrough

5. **Client Workstation Setup**
   - Windows 11 client configuration
   - Active Directory server preparation
   - RHEL client deployment
   - Connectivity verification

6. **Screened Subnet Deployment**
   - WebServer configuration
   - Database server setup
   - Firewall rule creation
   - Zone internet access enablement

7. **Security Configuration**
   - Firewall GUI access from Security Desktop
   - "Block private networks" issue resolution
   - Final rule implementation

### Key Challenges Solved

- ✅ Documentation vs. reality (lab environment differences)
- ✅ Virtual environment interface detection issues
- ✅ Bridge connectivity problems at infrastructure level
- ✅ Subnet conflicts requiring transit network design
- ✅ Multi-layer NAT configuration
- ✅ Firewall default-deny security policy management
- ✅ OPNsense "block private networks" feature conflict

## 📚 Documentation

### Available Documents

- **[Enterprise Network Build Report](docs/Enterprise_Network_Build_Report.docx)** - Comprehensive 25+ page detailed report
- **[Network Topology (Markdown)](docs/network_topology.md)** - Text-based topology reference
- **[Visual Network Diagram](diagrams/network_topology_diagram.html)** - Interactive HTML diagram
- **Configuration Examples** - Router and firewall configurations

### Documentation Highlights

The comprehensive build report includes:
- Detailed phase-by-phase configuration steps
- Challenge identification and resolution
- Technical lessons learned
- Process improvement insights
- Complete device inventory
- Traffic flow examples
- Next steps and recommendations

## 🛠️ Technologies Used

### Networking
- **VyOS** - Enterprise routing platform
- **OPNsense** - Open-source firewall/router
- **Proxmox** - Virtualization platform

### Operating Systems
- **Windows Server 2025** - Active Directory/DNS
- **Windows 11** - Client workstation
- **RHEL (Red Hat Enterprise Linux)** - Linux clients and servers
- **Kali Linux** - Security monitoring

### Protocols & Concepts
- Static routing
- Network Address Translation (NAT)
- Firewall rule management
- VLAN/Bridge segmentation
- Defense-in-depth architecture

## 🔄 Traffic Flow

### Example: Client to Internet

```
Client (192.168.1.100)
    ↓ Gateway: 192.168.1.254
Router-LAN eth4 (receives packet)
    ↓ NAT: 192.168.1.100 → 10.0.2.1
Router-LAN eth5 (10.0.2.1)
    ↓ Routes to: 10.0.2.2
Firewall vtnet2 (10.0.2.2)
    ↓ Routes internally
Firewall vtnet0 WAN (192.168.2.2)
    ↓ Gateway: 192.168.2.1
Router-WAN eth4 (192.168.2.1)
    ↓ NAT: 192.168.2.2 → 10.10.83.205
Router-WAN eth5 (10.10.83.205)
    ↓ Gateway: 10.10.0.1
Internet ✅
```

### Traffic Flow Principles

1. **Multi-layer NAT** - Two NAT translations (Router-LAN, Router-WAN)
2. **Firewall inspection** - All traffic inspected at zone boundaries
3. **Routing decisions** - Each hop makes independent routing choices
4. **Return path** - NAT tables maintain state for return traffic

## 💡 Lessons Learned

### Technical Insights

1. **Documentation vs. Reality** - Lab environments often differ from documentation; always verify
2. **Layer 2 vs. Layer 3 Troubleshooting** - Use ARP tables to distinguish physical vs. logical issues
3. **Virtual Environment Constraints** - Bridge assignments and link detection behave differently than physical
4. **Network Segmentation Planning** - Proper IP allocation prevents routing conflicts
5. **NAT Is Critical** - Without NAT, return traffic cannot find its way back to private IPs
6. **Default Security Is a Feature** - Firewall blocking is intentional; understand before disabling

### Process Improvements

1. **Systematic Approach** - Work outside-in (Internet → Router → Firewall → Clients)
2. **Iterative Problem-Solving** - Multiple approaches often needed; persistence pays off
3. **Detailed Documentation** - Essential for troubleshooting and future reference
4. **Test Each Layer** - Verify connectivity at each layer before proceeding

## 🚀 Future Enhancements

### High Priority
- [ ] Configure Active Directory Domain Services
- [ ] Join all workstations to domain
- [ ] Deploy web application on WebServer
- [ ] Configure database with schema
- [ ] Implement email services

### Medium Priority
- [ ] Deploy AI chatbot integration
- [ ] Set up network monitoring/analytics
- [ ] Configure automated backups
- [ ] Implement vulnerability scanning
- [ ] CSET security assessment

### Security Hardening
- [ ] Implement zone-to-zone access policies
- [ ] Configure comprehensive firewall logging
- [ ] Remove Security Desktop WAN GUI access
- [ ] Deploy IDS/IPS capabilities
- [ ] Implement VPN for remote access

## 📊 Project Metrics

| Metric | Value |
|--------|-------|
| **Total Configuration Time** | 1 Week |
| **Devices Configured** | 10 (100%) |
| **Network Zones** | 5 (Complete) |
| **Internet Connectivity** | 100% |
| **Major Issues Resolved** | 7+ |
| **Project Status** | ✅ OPERATIONAL |

## 👨‍💻 Author

**Justin**  
Network Engineer  
Enterprise Network Infrastructure Project

---

## 📝 License

This project documentation is provided for educational purposes.

## 🙏 Acknowledgments

- VyOS community for excellent routing platform
- OPNsense project for enterprise-grade firewall
- Proxmox for robust virtualization infrastructure

---

**Built with ❤️ and lots of troubleshooting**

*Last Updated: February 19, 2026*
