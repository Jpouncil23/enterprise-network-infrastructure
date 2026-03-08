# 📋 Network Quick Reference

## Device IP Addresses

### Infrastructure
```
Router-WAN (VM 677)
  - eth5: 10.10.83.205/16 (WAN - Internet)
  - eth4: 192.168.2.1/24 (LAN - To Firewall)

Firewall (VM 675)
  - vtnet0: 192.168.2.2/24 (WAN)
  - vtnet1: 10.0.1.1/24 (OPT1 - Screened Subnet)
  - vtnet2: 10.0.2.2/24 (LAN - Transit)

Router-LAN (VM 676)
  - eth5: 10.0.2.1/24 (To Firewall)
  - eth4: 192.168.1.254/24 (To Clients)

Security Desktop (VM 674)
  - eth0: 192.168.2.50/24
```

### Client Network (192.168.1.X)
```
AD/DNS Server (VM 691):    192.168.1.5
Windows 11 Client (VM 697): 192.168.1.100
RHEL Client (VM 678):       192.168.1.101
Gateway: 192.168.1.254
```

### Screened Subnet (10.0.1.X)
```
WebServer (VM 699):  10.0.1.100
Database (VM 680):   10.0.1.200
Gateway: 10.0.1.1
```

## Network Zones

| Network | Purpose | Gateway |
|---------|---------|---------|
| 10.10.X.X | Internet | 10.10.0.1 |
| 192.168.2.X | WAN Switch Zone | 192.168.2.1 |
| 10.0.2.X | Transit Network | 10.0.2.2 |
| 192.168.1.X | Client LAN | 192.168.1.254 |
| 10.0.1.X | Screened Subnet | 10.0.1.1 |

## Proxmox Bridges

| Bridge | Network | Connected Devices |
|--------|---------|------------------|
| maindhcp | 10.10.X.X | Router-WAN eth5 |
| nct17a1 | 192.168.2.X | Router-WAN eth4, Firewall WAN, Security Desktop |
| nct17a2 | 10.0.1.X | Firewall OPT1, WebServer, Database |
| nct17a3 | 10.0.2.X | Firewall LAN, Router-LAN eth5 |
| nct17a4 | 192.168.1.X | Router-LAN eth4, Clients, AD/DNS |

## Access Information

### Firewall Web GUI
- **URL**: https://192.168.2.2
- **Access From**: Security Desktop (192.168.2.50)
- **Credentials**: root / opnsense
- **Note**: WAN GUI access enabled for lab only

### Default DNS Servers
- **Primary**: 8.8.8.8 (Google)
- **Secondary**: 1.1.1.1 (Cloudflare)
- **Future**: 192.168.1.5 (AD/DNS after configuration)

## Common Commands

### VyOS (Router-WAN, Router-LAN)
```bash
# Enter configuration mode
configure

# Show interfaces
show interfaces

# Show routes
show ip route

# Show NAT rules
show nat source rules

# Show ARP table
show arp

# Save configuration
commit
save
exit
```

### OPNsense (Firewall)
```bash
# Shell access
Option 8 from console menu

# Check firewall status
pfctl -s info

# Show firewall rules
pfctl -sr

# Enable firewall
pfctl -e

# Disable firewall
pfctl -d

# Show NAT rules
pfctl -s nat
```

### Client Configuration
```bash
# Windows - Check IP
ipconfig /all

# Windows - Test connectivity
ping 8.8.8.8

# Linux - Check IP
ip addr show

# Linux - Test connectivity  
ping -c 3 8.8.8.8
```

## Troubleshooting Checklist

### No Internet Connectivity
1. ✅ Check IP configuration (ipconfig/ip addr)
2. ✅ Ping gateway (192.168.1.254 for clients)
3. ✅ Ping firewall (192.168.1.1 or 10.0.1.1)
4. ✅ Ping external IP (8.8.8.8)
5. ✅ Check NAT rules on routers
6. ✅ Check firewall rules on OPNsense
7. ✅ Verify routing tables

### Layer 2 Issues
- Check ARP table: `show arp` (VyOS) or `arp -a` (Windows/Linux)
- Verify bridge connectivity in Proxmox
- Check interface status: `show interfaces`

### Firewall Issues
- Check firewall logs in OPNsense GUI
- Verify firewall is enabled: `pfctl -s info`
- Review firewall rules: `pfctl -sr`
- Check if traffic is being blocked by default-deny

## Traffic Flow Example

**Client (192.168.1.100) → Internet:**
```
1. Client → Router-LAN (192.168.1.254)
2. Router-LAN NAT: 192.168.1.100 → 10.0.2.1
3. Router-LAN → Firewall (10.0.2.2)
4. Firewall → Router-WAN (192.168.2.1)
5. Router-WAN NAT: 192.168.2.2 → 10.10.83.205
6. Router-WAN → Internet
```

---

*Keep this file handy for quick reference during configuration and troubleshooting!*
