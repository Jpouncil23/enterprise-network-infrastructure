# 📋 Network Quick Reference

## Device IP Addresses

### Routers
```
Router-WAN (VyOS)
  eth5: 10.10.83.205/16 (WAN - Internet via DHCP)
  eth4: 192.168.2.1/24 (LAN - To Firewall)

Router-LAN (VyOS)
  eth5: 10.0.2.1/24 (To Firewall - Transit Network)
  eth4: 192.168.1.254/24 (To Clients)
```

### Firewall
```
Firewall (OPNsense)
  vtnet0 (WAN): 192.168.2.2/24
  vtnet1 (OPT1): 10.0.1.1/24 (Screened Subnet/DMZ)
  vtnet2 (LAN): 10.0.2.2/24 (Transit Network)
```

### Monitoring
```
Security Desktop (Kali): 192.168.2.50/24
  Gateway: 192.168.2.1
```

### Client LAN (192.168.1.X)
```
Windows Client:     192.168.1.100/24
RHEL Client:        192.168.1.101/24
Server:             192.168.1.5/24
Gateway:            192.168.1.254
```

### Screened Subnet (10.0.1.X)
```
WebServer:   10.0.1.100/24
Database:    10.0.1.200/24
Gateway:     10.0.1.1
```

## Network Zones

| Network | Purpose | Gateway | Bridge |
|---------|---------|---------|--------|
| 10.10.X.X | Internet | 10.10.0.1 | maindhcp |
| 192.168.2.X | WAN Switch / Security Zone | 192.168.2.1 | nct17a1 |
| 10.0.2.X | Transit (Firewall ↔ Router-LAN) | 10.0.2.2 | nct17a3 |
| 192.168.1.X | Client LAN | 192.168.1.254 | nct17a4 |
| 10.0.1.X | Screened Subnet / DMZ | 10.0.1.1 | nct17a2 |

## Traffic Flow

**Client (192.168.1.100) → Internet:**

```
1. Client sends to gateway 192.168.1.254 (Router-LAN eth4)
2. Router-LAN NAT: 192.168.1.100 → 10.0.2.1
3. Router-LAN forwards to 10.0.2.2 (Firewall transit)
4. Firewall inspects and routes to WAN
5. Firewall forwards to 192.168.2.1 (Router-WAN)
6. Router-WAN NAT: 192.168.2.2 → 10.10.83.205
7. Router-WAN forwards to 10.10.0.1 (Internet gateway)
8. ✅ Reaches Internet
```

## VyOS Commands

### Viewing Configuration
```bash
# Show interfaces
show interfaces

# Show IP addresses
show interfaces detail

# Show routing table
show ip route

# Show NAT rules
show nat source rules

# Show ARP table
show arp
```

### Configuration Mode
```bash
# Enter configuration
configure

# Example: Set interface IP
set interfaces ethernet eth4 address 192.168.1.254/24

# Example: Set default route
set protocols static route 0.0.0.0/0 next-hop 10.10.0.1

# Example: Configure NAT
set nat source rule 100 outbound-interface eth5
set nat source rule 100 source address 192.168.1.0/24
set nat source rule 100 translation address masquerade

# Save and exit
commit
save
exit
```

## OPNsense Commands

### Console Menu
```
Option 1: Assign interfaces (may not persist in virtual environments)
Option 2: Set interface IP address (recommended - persists)
Option 8: Shell access
```

### Shell Commands
```bash
# Check firewall status
pfctl -s info

# Show firewall rules
pfctl -sr

# Show NAT rules
pfctl -s nat

# Enable firewall
pfctl -e

# Disable firewall (troubleshooting only)
pfctl -d

# Show state table
pfctl -s states
```

### Web GUI
```
URL: https://192.168.2.2
Username: root
Password: opnsense
Access from: Security Desktop (192.168.2.50)
```

## Troubleshooting

### No Internet Connectivity

**Step 1:** Test local connectivity
```bash
# Ping gateway
ping 192.168.1.254

# Ping firewall (from client)
ping 192.168.1.1  # or appropriate gateway
```

**Step 2:** Test external connectivity
```bash
# Ping Google DNS
ping 8.8.8.8

# Ping by hostname (tests DNS)
ping google.com
```

**Step 3:** Check routing
```bash
# VyOS
show ip route

# Linux
ip route show

# Windows
route print
```

**Step 4:** Check NAT
```bash
# VyOS
show nat source rules
show nat source translations

# OPNsense
pfctl -s nat
```

**Step 5:** Check firewall rules
```bash
# OPNsense
pfctl -sr
pfctl -s info  # verify enabled
```

### Layer 2 Issues

**Check ARP tables:**
```bash
# VyOS
show arp

# Linux
arp -a
ip neigh show

# Windows
arp -a
```

**Symptoms:**
- ARP shows "(incomplete)" = Layer 2 problem
- ARP shows MAC address but ping fails = Layer 3/firewall issue

### Common Problems

**Subnet Conflict:**
- Symptom: Routing ambiguity, incorrect interface selection
- Check: Both interfaces on same subnet but different bridges?
- Solution: Use separate subnets for each network role

**Missing NAT:**
- Symptom: Can ping gateway but not internet
- Check: NAT rules configured on router?
- Solution: Add masquerade rule for source network

**Firewall Blocking:**
- Symptom: Works with `pfctl -d`, fails with firewall enabled
- Check: Firewall logs show blocks?
- Solution: Create allow rule for required traffic

**Configuration Not Persisting:**
- Symptom: Settings lost after reboot
- Check: Using correct configuration method?
- Solution: VyOS use `commit; save`, OPNsense use option 2

## Key Configuration Files

### VyOS
```
/config/config.boot  # Main configuration file
```

### OPNsense
```
/conf/config.xml  # Main configuration file
```

## DNS Servers

**Current:**
- Primary: 8.8.8.8 (Google)
- Secondary: 1.1.1.1 (Cloudflare)

## Important Notes

1. **Adapt designs to your environment** - Original documentation may call for more interfaces or networks than available, so adjust the architecture accordingly
2. **Check ARP first** - Distinguishes Layer 2 vs Layer 3 issues
3. **Transit networks** - Essential for clean multi-layer routing
4. **NAT is required** - For private networks to access internet
5. **Default-deny firewalls** - Blocking is a feature, not a bug
6. **Virtual quirks** - Interface detection may be unreliable

---

*Keep this handy during network configuration and troubleshooting!*
