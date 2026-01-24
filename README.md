# Packet Tracer Network Configurations

This repository contains Cisco IOS configuration files for a multi-site network topology designed for Cisco Packet Tracer. The network consists of three main locations: Delft (main site), Aruba (branch site), and an ISP router providing internet connectivity.

## 📋 Table of Contents

- [Network Overview](#network-overview)
- [Network Topology](#network-topology)
- [Directory Structure](#directory-structure)
- [Site Configurations](#site-configurations)
- [Key Features](#key-features)
- [IP Addressing Scheme](#ip-addressing-scheme)
- [VLANs](#vlans)
- [Security Features](#security-features)
- [Routing Protocols](#routing-protocols)
- [Usage Instructions](#usage-instructions)

## 🌐 Network Overview

This network implementation features:
- **Dual-stack networking** (IPv4 and IPv6)
- **Multi-site connectivity** with redundant links
- **BGP peering** between ISP and customer networks
- **OSPF routing** for internal networks
- **GRE tunnels** for site-to-site VPN connectivity
- **HSRP** (Hot Standby Router Protocol) for gateway redundancy
- **Layer 3 switching** with inter-VLAN routing
- **Comprehensive security** with AAA, SSH, and access control

## 📁 Directory Structure

```
Packet-Tracer-configs/
├── README.md                 # This file
├── ISP router.ios           # ISP edge router configuration
├── Aruba/                   # Aruba branch site configurations
│   ├── Router.ios           # Aruba site router
│   └── Switch.ios           # Aruba access switch
└── Delft/                   # Delft main site configurations
    ├── Router 1.ios         # Primary edge router
    ├── Router 2.ios         # Secondary edge router
    ├── MLS 1.ios            # Multi-Layer Switch 1 (primary)
    ├── MLS 2.ios            # Multi-Layer Switch 2 (secondary)
    ├── ALS1.ios             # Access Layer Switch 1
    └── ALS6.ios             # Access Layer Switch 6
```

## 🏢 Site Configurations

### ISP Router
- **Hostname:** KaiLaptop
- **Role:** Internet service provider edge router
- **WAN Links:** 
  - Serial0/1/0: 145.34.45.0/30 → Router 1 Delft
  - Serial0/1/1: 145.34.45.4/30 → Router 2 Delft
  - GigabitEthernet0/0/0: 145.34.46.0/30 → Router Aruba
- **Protocols:** BGP AS 100, NAT overload
- **IPv6:** Dual-stack with link-local addressing

### Delft Site
Main campus network with redundant routers and distribution layer switches.

#### Routers (1 & 2)
- **Primary/Secondary edge routers** with OSPF area 0
- **WAN connectivity** via BGP to ISP
- **GRE tunnels** to Aruba site
- **NAT** with access control lists
- **Inter-site redundancy** via Serial link

#### Multi-Layer Switches (MLS 1 & 2)
- **Layer 3 switching** with IP routing enabled
- **HSRP** for first-hop redundancy
- **Multiple VLANs:** Management (110), Marketing (120), Intranet (180), DMZ (190-192)
- **DHCP relay** (ip helper-address)
- **Policy-Based Routing** (PBR)
- **Access control lists** for inter-VLAN security

#### Access Layer Switches (ALS1, ALS6)
- **VLAN access** for end devices
- **STP BPDU Guard** on edge ports
- **Port security** on access interfaces

### Aruba Site
Branch office with simplified topology.

#### Router
- **Hostname:** Router-Aruba
- **WAN link:** 145.34.46.0/30 to ISP
- **LAN network:** 10.20.10.0/24
- **GRE tunnels** to both Delft routers
- **NAT** with exception lists
- **DHCP server** for VLAN 20
- **Default gateway:** Via ISP

#### Switch
- **Hostname:** Switch-Aruba
- **VLANs:** Marketing (210), Blackhole (999)
- **Access ports** for workstations and wireless APs
- **Rapid-PVST** spanning tree

## ✨ Key Features

### High Availability
- **HSRP** on MLS switches for gateway redundancy
- **Dual routers** at Delft site
- **Multiple GRE tunnels** between sites
- **Floating static routes** with administrative distance

### Security
- **AAA authentication** with local database
- **SSH access only** (no Telnet)
- **Console timeout** and logging synchronous
- **MOTD banner** warning unauthorized access
- **Access control lists** for NAT and inter-VLAN filtering
- **SNMP** read-only and read-write communities
- **Port security** with BPDU guard on edge ports
- **CDP disabled** on edge ports

### Services
- **DHCP server** on Aruba router
- **DHCP relay** on MLS switches
- **NTP synchronization** (10.10.80.11)
- **DNS** configuration (8.8.8.8)
- **SNMP monitoring** (community: R1/R1rw)

## 🔢 IP Addressing Scheme

### IPv4 Networks

#### Public (WAN) Networks
| Network | Usage | Location |
|---------|-------|----------|
| 145.34.45.0/30 | ISP ↔ Router 1 | WAN Link 1 |
| 145.34.45.4/30 | ISP ↔ Router 2 | WAN Link 2 |
| 145.34.46.0/30 | ISP ↔ Router Aruba | WAN Link 3 |
| 1.1.1.1/32 | Internet loopback | ISP |

#### Private (LAN) Networks - Delft
| Network | VLAN | Usage |
|---------|------|-------|
| 10.10.10.0/24 | 110 | Management |
| 10.10.20.0/24 | 120 | Marketing |
| 10.10.80.0/24 | 180 | Intranet |
| 192.168.0.0/30 | - | Router 1 ↔ MLS 1 |
| 192.168.0.8/30 | - | Router 1 ↔ MLS 2 |
| 192.168.0.16/30 | - | Router 1 ↔ Router 2 |

#### Private (LAN) Networks - Aruba
| Network | VLAN | Usage |
|---------|------|-------|
| 10.20.10.0/24 | 210 | Marketing |

#### Tunnel Networks
| Network | Usage |
|---------|-------|
| 172.16.0.0/30 | GRE: Aruba ↔ Router 1 |
| 172.16.0.4/30 | GRE: Aruba ↔ Router 2 |

### IPv6 Networks
- **Prefix:** 2001:f187::/32
- **Site prefixes:** :0:1010::/64, :0:1020::/64, :0:1080::/64, :0:2010::/64
- **Loopbacks:** 2001:db8:ffff::1/128 (ISP), 2001:f187:0:1::0/128 (Delft)
- **Link-local:** fe80::/10 for all WAN links

## 🏷️ VLANs

### Delft Site
| VLAN ID | Name | Network | Gateway (HSRP) |
|---------|------|---------|----------------|
| 110 | Management | 10.10.10.0/24 | 10.10.10.1 |
| 120 | Marketing | 10.10.20.0/24 | 10.10.20.1 |
| 180 | Intranet | 10.10.80.0/24 | 10.10.80.1 |
| 190 | DMZnet | - | - |
| 191 | External | - | - |
| 192 | Management | - | - |
| 999 | BLACKHOLE | - | Shutdown |

### Aruba Site
| VLAN ID | Name | Network | Gateway |
|---------|------|---------|---------|
| 210 | Marketing | 10.20.10.0/24 | 10.20.10.1 |
| 999 | BLACKHOLE | - | Shutdown |

## 🔒 Security Features

1. **Authentication:**
   - Enable secret: `class`
   - Local username: `user` / `cisc` (privilege 15)
   - AAA authentication for login and enable

2. **Remote Access:**
   - SSH only (transport input ssh)
   - Crypto key: 2048-bit RSA
   - Domain: hhs.nl

3. **Console Security:**
   - 60-minute timeout
   - Logging synchronous
   - Password protection

4. **Network Security:**
   - NAT with access control lists
   - Inter-VLAN ACLs on MLS switches
   - Blackhole VLAN (999) for unused ports
   - BPDU guard on access ports
   - CDP disabled on edge interfaces

## 🔄 Routing Protocols

### BGP (Border Gateway Protocol)
- **ISP AS:** 100
- **Delft AS:** 110
- **Used for:** Internet connectivity
- **Peering:** Dual-stack (IPv4 and IPv6)

### OSPF (Open Shortest Path First)
- **Area:** 0 (backbone)
- **Used at:** Delft site for internal routing
- **Passive interfaces:** Enabled by default (disabled on inter-switch/router links)
- **IPv6:** OSPFv3 enabled on all internal interfaces

### Static Routing
- **Default routes:** Aruba → ISP
- **Floating static routes:** For redundancy with AD 10/20
- **IPv6 static routes:** To/from Aruba site

### Policy-Based Routing (PBR)
- Configured on MLS switches
- Route-map: `PBR_TO_R1`

## 🚀 Usage Instructions

### Importing into Packet Tracer

1. **Open Cisco Packet Tracer**
2. **Create the network topology** matching the structure above
3. **Copy configuration files:**
   - For each device, enter CLI
   - Type `enable` then `copy paste` the entire .ios file content
   - Or use the "Load Script" feature in Packet Tracer

### Initial Setup Steps

1. **Configure ISP router first** (provides internet)
2. **Set up Delft routers** (establish WAN connectivity)
3. **Configure MLS switches** (enable VLANs and HSRP)
4. **Add access layer switches** (connect end devices)
5. **Configure Aruba site** (test site-to-site connectivity)

### Testing Connectivity

```bash
# Test inter-VLAN routing
ping 10.10.20.1 source vlan 110

# Test internet connectivity
ping 1.1.1.1

# Test site-to-site VPN
ping 10.20.10.1 source 10.10.10.1

# Verify IPv6
ping 2001:db8:ffff::1

# Check routing
show ip route
show ipv6 route

# Verify HSRP
show standby brief

# Check BGP neighbors
show ip bgp summary
```

### Important Notes

- **SSH Keys:** You may need to regenerate RSA keys after importing
- **Time sync:** Configure NTP server at 10.10.80.11
- **DHCP:** Ensure DHCP server/relay is working for dynamic addressing
- **Passwords:**
  - Enable secret: `class`
  - User: `user` / `cisc`
- **SNMP Communities:** R1 (RO), R1rw (RW)

## 📝 Configuration Standards

All devices follow these standards:
- ✅ Hostname configured
- ✅ Domain lookup disabled
- ✅ MOTD banner set
- ✅ Console timeout: 60 minutes
- ✅ SSH enabled (2048-bit RSA)
- ✅ AAA authentication
- ✅ Logging synchronous
- ✅ SNMP monitoring enabled
- ✅ HTTP/HTTPS servers disabled

## 📄 License

This project is for educational purposes.

---

**Authors:** Max Blaauw, Arne Jansonius, Kai Diemel & Ole Spiegelenberg  
**Organization:** HHS (The Hague University of Applied Sciences)  
**Domain:** hhs.nl