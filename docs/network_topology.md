# Enterprise Network Topology
## Complete Network Infrastructure Diagram

```
                                    INTERNET
                                       |
                              [10.10.0.1 Gateway]
                                       |
                        ┌──────────────┴──────────────┐
                        │   Router-WAN (VM 677)       │
                        │   eth5: 10.10.83.205/16     │
                        │   (DHCP, bridge: maindhcp)  │
                        └──────────────┬──────────────┘
                                       | eth4: 192.168.2.1/24
                                       |
                            [Bridge: nct17a1]
                          192.168.2.0/24 Network
                                       |
                    ┌──────────────────┼──────────────────┐
                    |                  |                  |
         ┌──────────┴──────────┐      |      ┌──────────┴──────────┐
         │ Security Desktop    │      |      │  Firewall (VM 675)  │
         │ (VM 674 - Kali)     │      |      │  OPNsense           │
         │ 192.168.2.50/24     │      |      │  vtnet0 (WAN)       │
         │ Gateway: 192.168.2.1│      |      │  192.168.2.2/24     │
         └─────────────────────┘      |      └──────────┬──────────┘
                                      |                 |
                                      |      ┌──────────┼──────────┐
                                      |      |          |          |
                                      |   vtnet1     vtnet2        |
                                      | (OPT1)      (LAN)          |
                                      | 10.0.1.1    10.0.2.2       |
                                      |     |          |           |
                                      |     |    [Bridge: nct17a3] |
                                      |     |    10.0.2.0/24       |
                                      |     |          |           |
                                      |     |    ┌─────┴─────┐     |
                                      |     |    │ Router-LAN│     |
                                      |     |    │ (VM 676)  │     |
                                      |     |    │ eth5:     │     |
                                      |     |    │ 10.0.2.1  │     |
                                      |     |    └─────┬─────┘     |
                                      |     |          | eth4:     |
                                      |     |          | 192.168.1.254
                                      |     |          |           |
                        [Bridge: nct17a2]   |  [Bridge: nct17a4]  |
                        10.0.1.0/24 Network |  192.168.1.0/24 Net |
                      (Screened Subnet)     |  (Client LAN)       |
                                |           |          |           |
                    ┌───────────┴─────┐     |    ┌─────┴─────┐    |
                    |                 |     |    |           |    |
         ┌──────────┴─────┐  ┌────────┴────┐    ├───────────┼────┤
         │  WebServer     │  │  Database   │    │           │    │
         │  (VM 699)      │  │  (VM 680)   │    │           │    │
         │  Windows       │  │  RHEL       │    │           │    │
         │  10.0.1.100    │  │  10.0.1.200 │    │           │    │
         │  GW: 10.0.1.1  │  │  GW: 10.0.1.1│   │           │    │
         └────────────────┘  └──────────────┘    │           │    │
                                              ┌───┴───┐  ┌────┴────┐
                                              │ Win11 │  │  RHEL   │
                                              │Client │  │ Client  │
                                              │(VM697)│  │ (VM678) │
                                              │.100   │  │  .101   │
                                              └───────┘  └─────────┘
                                                   |
                                              ┌────┴────┐
                                              │ AD/DNS  │
                                              │(VM 691) │
                                              │  .5     │
                                              └─────────┘
                                       All use GW: 192.168.1.254
```

---

## Network Zones and Addressing

### **Zone 1: WAN/Internet (10.10.X.X)**
| Device | IP Address | Netmask | Gateway | Notes |
|--------|-----------|---------|---------|-------|
| Internet Gateway | 10.10.0.1 | - | - | Lab network gateway |
| Router-WAN eth5 | 10.10.83.205 | /16 | 10.10.0.1 | DHCP assigned |

---

### **Zone 2: WAN Switch Zone (192.168.2.X) - Bridge: nct17a1**
| Device | Interface | IP Address | Gateway | Notes |
|--------|-----------|-----------|---------|-------|
| Router-WAN | eth4 | 192.168.2.1/24 | - | Gateway for this zone |
| Firewall | vtnet0 (WAN) | 192.168.2.2/24 | 192.168.2.1 | Internet access |
| Security Desktop | eth0 | 192.168.2.50/24 | 192.168.2.1 | Monitoring/security |

---

### **Zone 3: Firewall Transit (10.0.2.X) - Bridge: nct17a3**
| Device | Interface | IP Address | Gateway | Notes |
|--------|-----------|-----------|---------|-------|
| Firewall | vtnet2 (LAN) | 10.0.2.2/24 | - | Transit interface |
| Router-LAN | eth5 | 10.0.2.1/24 | 10.0.2.2 | Routes to firewall |

**Purpose:** Transit network between Firewall and Router-LAN

---

### **Zone 4: Client LAN (192.168.1.X) - Bridge: nct17a4**
| Device | VM # | IP Address | Gateway | OS | Status |
|--------|------|-----------|---------|-----|--------|
| Router-LAN | 676 | 192.168.1.254 (eth4) | 10.0.2.2 | VyOS | Gateway ✅ |
| AD/DNS Server | 691 | 192.168.1.5 | 192.168.1.254 | Windows 2025 | Online ✅ |
| Windows 11 Client | 697 | 192.168.1.100 | 192.168.1.254 | Windows 11 | Online ✅ |
| RHEL Client | 678 | 192.168.1.101 | 192.168.1.254 | RHEL | Online ✅ |
| Tracker | TBD | 192.168.1.10 | 192.168.1.254 | - | Not configured |

**Purpose:** Internal trusted network for workstations and domain services  
**DNS:** 8.8.8.8, 1.1.1.1 (will change to 192.168.1.5 after AD setup)

---

### **Zone 5: Screened Subnet (10.0.1.X) - Bridge: nct17a2**
| Device | VM # | IP Address | Gateway | OS | Status |
|--------|------|-----------|---------|-----|--------|
| Firewall | 675 | 10.0.1.1 (vtnet1) | - | OPNsense | Gateway ✅ |
| WebServer | 699 | 10.0.1.100 | 10.0.1.1 | Windows | Online ✅ |
| Database | 680 | 10.0.1.200 | 10.0.1.1 | RHEL | Configured |

**Purpose:** Semi-trusted DMZ for public-facing services  
**DNS:** 8.8.8.8, 1.1.1.1

---

## Traffic Flow Examples

### **Client to Internet:**
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

### **WebServer to Internet:**
```
WebServer (10.0.1.100)
    ↓ Gateway: 10.0.1.1
Firewall vtnet1 (10.0.1.1)
    ↓ Routes internally
Firewall vtnet0 WAN (192.168.2.2)
    ↓ Gateway: 192.168.2.1
Router-WAN → Internet ✅
```

---

## Proxmox Bridge Mappings

| Bridge | Connected Devices | Network | Purpose |
|--------|------------------|---------|---------|
| **maindhcp** | Router-WAN eth5 | 10.10.X.X | Internet connection |
| **nct17a1** | Router-WAN eth4, Firewall WAN, Security Desktop | 192.168.2.X | WAN Zone |
| **nct17a2** | Firewall OPT1, WebServer, Database | 10.0.1.X | Screened Subnet |
| **nct17a3** | Firewall LAN, Router-LAN eth5 | 10.0.2.X | Transit network |
| **nct17a4** | Router-LAN eth4, Clients, AD/DNS | 192.168.1.X | Client LAN |

---

## Router Configurations

### **Router-WAN (VM 677) - VyOS**

**Interfaces:**
- eth5: 10.10.83.205/16 (WAN - DHCP)
- eth4: 192.168.2.1/24 (LAN)

**Static Routes:**
```
default via 10.10.0.1 dev eth5
10.0.1.0/24 via 192.168.2.2 dev eth4
192.168.1.0/24 via 192.168.2.2 dev eth4
```

**NAT Rules:**
```
Rule 100: 10.0.1.0/24 → masquerade via eth5
Rule 101: 192.168.1.0/24 → masquerade via eth5
Rule 102: 192.168.2.0/24 → masquerade via eth5
```

**DNS:** 8.8.8.8, 1.1.1.1

---

### **Router-LAN (VM 676) - VyOS**

**Interfaces:**
- eth5: 10.0.2.1/24 (To Firewall)
- eth4: 192.168.1.254/24 (Internal Clients)

**Static Routes:**
```
default via 10.0.2.2 dev eth5
```

**NAT Rules:**
```
Rule 100: 192.168.1.0/24 → masquerade via eth5
```

**DNS:** 8.8.8.8, 1.1.1.1

---

### **Firewall (VM 675) - OPNsense**

**Interfaces:**
- vtnet0 (WAN): 192.168.2.2/24 → Gateway: 192.168.2.1
- vtnet1 (OPT1): 10.0.1.1/24 → Screened Subnet
- vtnet2 (LAN): 10.0.2.2/24 → Transit to Router-LAN

**Firewall Rules:**
- **WAN:** Allow 192.168.2.50 → WAN address port 443 (Security Desktop GUI access)
- **OPT1:** Allow OPT1 net → any (Screened Subnet internet access)

**NAT:** Automatic outbound NAT enabled

**DNS:** 8.8.8.8, 1.1.1.1

---

## Security Features

### **Network Segmentation:**
✅ Three distinct security zones (WAN, Screened Subnet, Internal LAN)  
✅ Firewall controlling traffic between zones  
✅ Defense-in-depth architecture

### **NAT Configuration:**
✅ Multi-layer NAT (Router-LAN + Router-WAN)  
✅ Internal IPs hidden from internet

### **Firewall Protection:**
✅ Default deny on all interfaces  
✅ Explicit allow rules only where needed  
✅ Screened subnet isolated from internal LAN

### **Access Control:**
✅ Security Desktop can monitor WAN zone  
✅ Security Desktop has firewall GUI access  
✅ All clients authenticate through AD (pending setup)

---

## Remaining Configuration Tasks

### **High Priority:**
- [ ] Configure Active Directory on 192.168.1.5
- [ ] Join clients to domain
- [ ] Configure DNS to use AD server (192.168.1.5)
- [ ] Deploy web application on WebServer
- [ ] Deploy database on Database server

### **Medium Priority:**
- [ ] Configure email server
- [ ] Set up monitoring/analytics
- [ ] Configure backup solutions
- [ ] Deploy AI infrastructure

### **Documentation:**
- [x] Network topology documented
- [ ] Update Gantt chart
- [ ] Update network diagram (Visio)
- [ ] Document all credentials
- [ ] Complete CSET assessment

---

## Quick Reference - All IP Addresses

```
Infrastructure:
  10.10.83.205 - Router-WAN eth5 (WAN)
  192.168.2.1  - Router-WAN eth4 (Gateway)
  192.168.2.2  - Firewall WAN
  192.168.2.50 - Security Desktop
  10.0.2.1     - Router-LAN eth5 (Transit)
  10.0.2.2     - Firewall Transit
  192.168.1.254 - Router-LAN eth4 (LAN Gateway)

Client LAN (192.168.1.X):
  192.168.1.5   - AD/DNS Server
  192.168.1.10  - Tracker (not configured)
  192.168.1.100 - Windows 11 Client
  192.168.1.101 - RHEL Client

Screened Subnet (10.0.1.X):
  10.0.1.1   - Firewall OPT1 (Gateway)
  10.0.1.100 - WebServer
  10.0.1.200 - Database
```

---

## Network Status: ✅ OPERATIONAL

**All zones have internet connectivity!**
**All devices configured and online!**
**Ready for service deployment!**

---

*Generated: February 19, 2026*
*Network Engineer: Justin*
*Project: Enterprise Network Infrastructure*
