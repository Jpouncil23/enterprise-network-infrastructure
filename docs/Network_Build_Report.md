# Enterprise Network Infrastructure Build

## Network Configuration Report

**VyOS Routers • OPNsense Firewall • Multi-Zone Architecture**

**February 2026**

**Justin - Network Engineer**

---

## Executive Summary

This report documents the complete build process of an enterprise network infrastructure from scratch, covering router configuration, firewall deployment, multi-zone architecture implementation, and systematic troubleshooting of connectivity issues.

### What Was Built

Over the course of one week, a complete five-zone network was configured with:

- Two VyOS routers providing multi-layer routing
- OPNsense firewall with three interfaces for zone segmentation
- Five distinct network zones with proper security isolation
- Multi-layer NAT configuration for address translation
- Ten configured devices with 100% internet connectivity

---

## Phase 1: Router-WAN Configuration

### Initial Setup

The network build began with configuring the WAN router using VyOS. This router serves as the primary gateway to the internet and the first routing layer in the architecture.

### Challenge: Adapting the Network Design

The original documentation specified 3 different IP address schemes across 3 network interfaces, including a 172.31.0.X network. However, the lab environment only provided 2 usable interfaces on the WAN router, making the third network impossible to implement as designed.

The solution was to adapt the network architecture to work within the 2-interface constraint while preserving the same overall security zone design. The 172.x.x.x network was removed, and the remaining architecture was consolidated to maintain similar segmentation using the available interfaces. The eth5 interface was configured to use DHCP and successfully obtained IP address 10.10.83.205/16.

### Final Configuration

| Interface | IP Address | Purpose |
|-----------|-----------|---------|
| eth5 | 10.10.83.205/16 (DHCP) | WAN - Internet Connection |
| eth4 | 192.168.2.1/24 | LAN - To Firewall |

### Routing and NAT

Static routes were configured for internal networks, and NAT masquerading was implemented for all internal zones to enable internet access.

---

## Phase 2: OPNsense Firewall Configuration

### Deployment Challenge

The firewall configuration presented significant challenges. Initial attempts to configure interfaces using console menu option 1 (Assign interfaces) appeared successful but the IP assignments did not persist after configuration.

### Root Cause and Solution

The virtual environment in Proxmox does not report reliable network link status, causing OPNsense's auto-detection to fail. The solution was to use console menu option 2 (Set interface IP address) instead, which writes configuration directly to the config file and persists across reboots.

### Final Configuration

| Interface | IP Address | Bridge | Purpose |
|-----------|-----------|--------|---------|
| vtnet0 | 192.168.2.2/24 | nct17a1 | WAN |
| vtnet1 | 10.0.1.1/24 | nct17a2 | Screened Subnet |
| vtnet2 | 10.0.2.2/24 | nct17a3 | Transit |

---

## Phase 3: Router-LAN and Transit Network

### Critical Discovery: Subnet Conflict

The most significant technical challenge occurred during Router-LAN configuration. Initially, both Router-LAN interfaces were configured on the 192.168.1.X subnet:

- eth5: 192.168.1.2 (connected to firewall via bridge nct17a3)
- eth4: 192.168.1.254 (connected to clients via bridge nct17a4)

This created routing ambiguity where the router could not determine which interface should handle traffic for the 192.168.1.X network.

### Solution: Transit Network Design

A dedicated transit network (10.0.2.X) was created for communication between the firewall and Router-LAN. The firewall's vtnet2 interface was reconfigured from 192.168.1.1 to 10.0.2.2, and Router-LAN's eth5 was changed from 192.168.1.2 to 10.0.2.1.

| Interface | IP Address | Bridge | Purpose |
|-----------|-----------|--------|---------|
| eth5 | 10.0.2.1/24 | nct17a3 | To Firewall |
| eth4 | 192.168.1.254/24 | nct17a4 | To Clients |

### NAT Configuration

NAT was configured on Router-LAN to translate client traffic from 192.168.1.X to 10.0.2.1 when forwarding to the firewall. This enabled proper routing and return traffic handling.

---

## Phase 4: Client Network Configuration

### Client Devices

Three client devices were configured on the 192.168.1.X network:

- Windows 11 Client: 192.168.1.100/24
- RHEL Client: 192.168.1.101/24
- Windows Server: 192.168.1.5/24

### Connectivity Challenge

Initial testing revealed clients could ping their gateway (192.168.1.254) but could not reach the internet (8.8.8.8). This indicated local connectivity was working but routing to the internet was failing.

### Root Cause: Missing NAT

Router-LAN was not performing NAT on traffic from the client network. Without NAT, packets from 192.168.1.100 reached the firewall with their original source IP, and return traffic could not be routed back.

After configuring NAT masquerade on Router-LAN's outbound interface (eth5) for source network 192.168.1.0/24, all clients achieved full internet connectivity.

---

## Phase 5: Screened Subnet Configuration

### DMZ Servers

Two servers were configured in the screened subnet (DMZ):

- WebServer (Windows): 10.0.1.100/24
- Database (RHEL): 10.0.1.200/24

### Firewall Blocking Challenge

Servers could not reach the internet when the OPNsense firewall was enabled. Disabling packet filtering (pfctl -d) immediately resolved connectivity, confirming the firewall was the cause.

### Solution: Firewall Rule Creation

OPNsense implements a default-deny security policy. A firewall rule was created via the web GUI to allow traffic from the OPT1 interface (screened subnet) to the internet. After applying this rule, both DMZ servers achieved full internet connectivity with the firewall enabled.

---

## Phase 6: Firewall Management Access

### GUI Access Configuration

To enable firewall management from the Security Desktop, GUI access needed to be configured from the WAN interface.

### Challenge: Block Private Networks Feature

After creating a firewall rule allowing traffic from 192.168.2.50 (Security Desktop) to the firewall's WAN address on port 443, access still failed. Firewall logs showed 'Block private networks from WAN' was rejecting the traffic.

### Solution

OPNsense includes a security feature that blocks RFC1918 private IP addresses from entering the WAN interface to prevent IP spoofing. This feature was disabled on the WAN interface specifically for this lab environment, as the Security Desktop legitimately exists on a private IP in the WAN zone. Additionally, packet filtering was enabled (pfctl -e) to ensure rules were being enforced.

---

## Traffic Flow Analysis

### Client to Internet Path

Understanding the complete traffic flow helps illustrate how the multi-layer architecture functions:

1. Client (192.168.1.100) sends packet to gateway 192.168.1.254 (Router-LAN eth4)
2. Router-LAN performs NAT: Source 192.168.1.100 becomes 10.0.2.1
3. Router-LAN eth5 forwards packet to firewall at 10.0.2.2 (transit network)
4. Firewall vtnet2 receives packet, inspects against rules
5. Firewall routes packet internally to WAN interface
6. Firewall vtnet0 forwards to Router-WAN at 192.168.2.1
7. Router-WAN eth4 receives packet
8. Router-WAN performs second NAT: Source becomes 10.10.83.205
9. Router-WAN eth5 forwards to Internet gateway at 10.10.0.1
10. Packet reaches the Internet

Return traffic follows the reverse path using NAT state tables maintained by both routers.

---

## Technical Lessons Learned

### 1. Adapting Designs to Real-World Constraints

The original documentation called for 3 separate IP address schemes, but the lab only had 2 available interfaces on the WAN router. Knowing how to adapt a network design to work within hardware limitations while preserving the core architecture and security zones is a critical skill. Evaluate what can be consolidated or removed without compromising the overall design.

### 2. Layer 2 vs Layer 3 Troubleshooting

Use ARP tables to distinguish between physical connectivity issues and logical routing problems. When ARP shows '(incomplete)', this indicates a Layer 2 problem. When ARP shows a MAC address but pings fail, this indicates a Layer 3 or firewall issue.

### 3. Subnet Planning

Proper IP address allocation prevents routing conflicts. Avoid placing multiple network functions on the same subnet when they connect through different physical or virtual bridges. Transit networks between routing layers eliminate ambiguity.

### 4. NAT Necessity

Without NAT, upstream devices cannot route return traffic to private IP addresses. Symptoms appear as one-way connectivity where local network functions but internet access fails. Multi-layer NAT is essential when routing through multiple private network segments.

### 5. Default Security Behaviors

Firewall blocking behavior and default-deny policies are security features, not configuration errors. Understanding these intentional behaviors prevents unnecessary troubleshooting and helps maintain proper security posture.

### 6. Virtual Environment Constraints

Virtual infrastructure has different characteristics than physical hardware. Interface link detection may be unreliable, bridge assignments may be locked, and certain configuration methods may persist better than others. Adapt solutions to work within these constraints rather than fighting them.

---

## Final Network Topology

### Network Zones

| Zone | Network | Bridge | Purpose | Gateway |
|------|---------|--------|---------|---------|
| WAN/Internet | 10.10.X.X | maindhcp | Internet | 10.10.0.1 |
| WAN Switch | 192.168.2.X | nct17a1 | Security Zone | 192.168.2.1 |
| Transit | 10.0.2.X | nct17a3 | Firewall-Router | 10.0.2.2 |
| Client LAN | 192.168.1.X | nct17a4 | Internal Network | 192.168.1.254 |
| Screened Subnet | 10.0.1.X | nct17a2 | DMZ | 10.0.1.1 |

### Device Inventory

| Device | IP Address | Platform | Status |
|--------|-----------|----------|--------|
| Router-WAN | 10.10.83.205, 192.168.2.1 | VyOS | Online |
| Firewall | 192.168.2.2, 10.0.2.2, 10.0.1.1 | OPNsense | Online |
| Router-LAN | 10.0.2.1, 192.168.1.254 | VyOS | Online |
| Security Desktop | 192.168.2.50 | Kali Linux | Online |
| Windows Client | 192.168.1.100 | Windows 11 | Online |
| RHEL Client | 192.168.1.101 | RHEL | Online |
| Server | 192.168.1.5 | Win Server | Online |
| WebServer | 10.0.1.100 | Windows | Online |
| Database | 10.0.1.200 | RHEL | Online |

---

## Conclusion

This project successfully implemented a complete enterprise network infrastructure with proper security segmentation and full internet connectivity. Through systematic configuration and persistent troubleshooting, all networking components are operational.

### Final Metrics

| Metric | Value |
|--------|-------|
| Build Duration | 1 Week |
| Devices Configured | 10 (100%) |
| Network Zones | 5 (Complete) |
| Internet Connectivity | 100% |
| Project Status | OPERATIONAL |

The network infrastructure demonstrates practical application of enterprise networking principles including network segmentation, multi-layer routing, NAT configuration, and firewall management. All major challenges were systematically resolved through methodical troubleshooting.

---

**Network Engineer: Justin**

**February 2026**
