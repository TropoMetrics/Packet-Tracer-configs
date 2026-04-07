# Packet Tracer Network Configurations

A comprehensive enterprise network topology for Cisco Packet Tracer featuring dual-stack networking, redundant routing, site-to-site VPN, and enterprise-grade security. This multi-site network includes Delft (main campus), Aruba (branch office), Melbourne (international office), and ISP connectivity with BGP, OSPF, and HSRP.

## Features

### Multi-Site Architecture
- **Four-Site Topology**: Delft main campus, Aruba branch office, Melbourne international office, and ISP router
- **Redundant Design**: Dual edge routers at Delft with automatic failover
- **Site-to-Site VPN**: IPsec/GRE tunnels connecting all locations
- **Scalable Infrastructure**: Layer 3 switching with inter-VLAN routing
- **Global Presence**: Multi-continent network spanning Europe, Caribbean, and Australia

### Routing & Switching
- **BGP (AS 100/110)**: Internet connectivity with dual-stack peering
- **OSPF Area 0**: Internal routing at Delft site
- **HSRP Gateway Redundancy**: Automatic failover for first-hop routing
- **Policy-Based Routing**: Traffic engineering on MLS switches
- **Static Routes**: Floating routes with administrative distance for backup paths

### Security Features
- **AAA Authentication**: Centralized authentication with local database
- **SSH-Only Access**: 2048-bit RSA keys, no Telnet
- **Access Control Lists**: NAT filtering and inter-VLAN security
- **Port Security**: BPDU guard on edge ports, CDP disabled on untrusted interfaces
- **VLAN Segmentation**: Blackhole VLAN (999) for unused ports
- **Banner Warnings**: MOTD configured on all devices

### Network Services
- **DHCP Services**: Server on Aruba router, relay on MLS switches
- **NTP Synchronization**: Time services at 10.10.80.11
- **SNMP Monitoring**: Read-only and read-write communities configured
- **DNS Configuration**: Google DNS (8.8.8.8) for name resolution
- **NAT Overload**: PAT for internet connectivity with exception lists

### Dual-Stack Networking
- **IPv4 & IPv6**: Full dual-stack implementation across all sites
- **IPv6 OSPFv3**: Native IPv6 routing at Delft
- **Link-Local Addressing**: fe80::/10 on all WAN links
- **IPv6 Static Routes**: Configured for Aruba site connectivity

## Getting Started

### Prerequisites

- **Cisco Packet Tracer** (v8.0 or higher) - [Download from Cisco NetAcad](https://www.netacad.com/courses/packet-tracer)
- Basic understanding of networking concepts
- Knowledge of Cisco IOS commands

### Quick Start

1. **Clone the repository**
```bash
   git clone https://github.com/TropoMetrics/Packet-Tracer-configs.git
   cd Packet-Tracer-configs
   ```

2. **Open Cisco Packet Tracer**
   - Launch Packet Tracer application
   - Create a new project or open existing topology

3. **Build the network topology**
   - Add devices matching the topology diagram below
   - Connect devices according to the network design
   - Configure device names and interfaces

4. **Apply configurations**
   - For each device, enter CLI mode
   - Copy the contents of the corresponding `.ios` file
   - Paste into the device CLI
   - Or use Packet Tracer's "Load Script" feature

5. **Verify connectivity**
```bash
   # Test basic connectivity
   ping 1.1.1.1
   
   # Check routing
   show ip route
   show ipv6 route
   
   # Verify BGP
   show ip bgp summary
   
   # Check HSRP status
   show standby brief
   ```

### Configuration Order

For best results, configure devices in this sequence:

1. **ISP Router** - Establishes internet connectivity
2. **Router 1 & Router 2 (Delft)** - Creates WAN connections and BGP peering
3. **C-MLS 1 & C-MLS 2 (Core Multi-Layer Switches)** - Sets up VLANs, HSRP, and internal routing
4. **ALS1-6 (Access Layer Switches)** - Configures access layer ports
5. **Aruba Router & Switch** - Completes branch office setup with VPN tunnels
6. **Melbourne Router & Switch** - Configures international office with VPN connectivity

## Network Topology

```
                    Internet (1.1.1.1/32)
                    2001:db8:ffff::1/128
                           |
                      [ISP Router]
                      (BGP AS 100)
                    /      |      \
         (Serial) /        |        \ (GigE)
        145.34.45.0/30     |         145.34.46.0/30
                /          |          \
               /           |           \
        [Router 1]    [Router 2]    [Router Aruba]
         (Delft)       (Delft)         (Aruba)
        BGP AS 110    BGP AS 110      10.20.10.0/24
            |            |             VLAN 210
        OSPF Area 0  OSPF Area 0          |
            |            |          [Switch-Aruba]
       [C-MLS 1]    [C-MLS 2]             |
      (Primary)   (Secondary)      [End Devices]
         HSRP         HSRP
          |            |
    [ALS1-6]     [Access Layer]        [Router Melbourne]
          |            |                   (Australia)
    [End Devices] [End Devices]       10.30.10.0/24
                                          VLAN 310

    VLANs Delft:
    - STAFFNET: 110-160 (Management, Marketing, Support, IT, Dev, Meteo)
    - CALCNET: 170 (Compute Cluster)
    - INTRANET: 180 (Internal Services)
    - DMZNET: 190-192 (DMZ & Management)
    
    VPN Tunnels:
    - Aruba ↔ Delft1/Delft2 (GRE/IPsec)
    - Melbourne ↔ Delft1/Delft2 (GRE/IPsec)
```

## Project Structure

```
Packet-Tracer-configs/
├── README.md                 # This documentation
├── ISP router.ios           # ISP edge router configuration
├── Aruba/                   # Aruba branch site
│   ├── Router.ios           #   - Site router with GRE tunnels
│   └── Switch.ios           #   - Access switch (VLAN 210)
└── Delft/                   # Delft main campus
    ├── Router 1.ios         #   - Primary edge router (BGP/OSPF)
    ├── Router 2.ios         #   - Secondary edge router (redundancy)
    ├── MLS 1.ios            #   - Distribution switch 1 (HSRP primary)
    ├── MLS 2.ios            #   - Distribution switch 2 (HSRP secondary)
    ├── ALS1.ios             #   - Access switch 1
    └── ALS6.ios             #   - Access switch 6
```

## Directory Structure

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

## Site Configurations

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

#### Multi-Layer Switches (C-MLS 1 & C-MLS 2)

- **Layer 3 switching** with IP routing enabled
- **HSRP** for first-hop redundancy (C-MLS 1 is primary)
- **Multiple VLANs:** STAFFNET (110-160), CALCNET (170), INTRANET (180), DMZNET (190-192)
- **DHCP relay** (IP helper-address to 10.10.80.11)
- **Policy-Based Routing** (PBR) for traffic engineering
- **Access control lists** for inter-VLAN security
- **Loopback addresses:** 192.168.255.50 (C-MLS 1), 192.168.255.51 (C-MLS 2)

#### Access Layer Switches (ALS1-6)

- **VLAN access** for end devices across six access switches
- **STP BPDU Guard** on edge ports
- **Port security** on access interfaces
- **Loopback addresses:** 192.168.255.100-105 (ALS1-6)

### Aruba Site
Branch office with simplified topology.

#### Router
- **Hostname:** Router-Aruba
- **WAN link:** 145.34.46.0/30 to ISP
- **LAN network:** 10.20.10.0/24
- **GRE tunnels** to both Delft routers
- **NAT** with exception lists
- **DHCP server** for VLAN 210
- **Default gateway:** Via ISP

#### Switch

- **Hostname:** Switch-Aruba
- **VLANs:** Marketing (210), Blackhole (999)
- **Access ports** for workstations and wireless APs
- **Rapid-PVST** spanning tree
- **Loopback address:** 192.168.254.100

### Melbourne Site

International office in Australia with similar topology to Aruba.

#### Router

- **Hostname:** Router-Melbourne
- **WAN link:** To ISP (configuration pending)
- **LAN network:** 10.30.10.0/24
- **GRE/IPsec tunnels** to both Delft routers
- **NAT** with exception lists
- **DHCP server** for VLAN 310
- **Loopback address:** 192.168.253.0

#### Switch

- **Hostname:** Switch-Melbourne
- **VLANs:** Marketing (310), Blackhole (999)
- **Access ports** for workstations and wireless APs
- **Rapid-PVST** spanning tree
- **Loopback address:** 192.168.253.100

## Key Features

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

## � Technology Stack

### Network Devices

- **Routers**: Cisco 4331 or 2911 series (or Packet Tracer equivalent)
- **Core Multi-Layer Switches**: Cisco 3650 or 3560 series (C-MLS 1 & 2)
- **Access Switches**: Cisco 2960 series (ALS1-6)
- **WAN Links**: Serial and GigabitEthernet interfaces

### Routing Protocols

- **BGP v4**: External routing (AS 100 ↔ AS 110)
- **OSPF v2/v3**: Internal routing (area 0) at Delft site
- **Static Routes**: Backup paths and default routes for branch offices
- **Policy-Based Routing**: Traffic engineering on C-MLS switches

### VPN & Security

- **IPsec**: Site-to-site VPN encryption
- **GRE Tunnels**: Overlay tunnels for VPN connectivity
- **BGP over VPN**: Dynamic routing between sites
- **AAA**: Authentication, Authorization, Accounting
- **ACLs**: Extended access control lists
- **NAT/PAT**: Network address translation

### Network Services

- **HSRP v2**: First-hop redundancy protocol
- **DHCP**: Dynamic addressing (server & relay)
- **NTP**: Network time synchronization (10.10.80.11)
- **SNMP v2c**: Monitoring and management
- **SSH v2**: Secure remote access (2048-bit RSA)
- **DNS**: Domain name resolution (8.8.8.8)

### Network Segmentation

- **13 VLANs** across all sites:
  - Delft: 12 VLANs (110-160, 170, 180, 190-192, 999)
  - Aruba: 2 VLANs (210, 999)
  - Melbourne: 2 VLANs (310, 999)

## � IP Addressing Scheme

### IPv4 Networks

#### Public (WAN) Networks

| Network | Usage | Location |
|---------|-------|----------|
| 145.34.45.0/30 | ISP ↔ Router 1 | WAN Link 1 |
| 145.34.45.4/30 | ISP ↔ Router 2 | WAN Link 2 |
| 145.34.46.0/30 | ISP ↔ Router Aruba | WAN Link 3 |
| 1.1.1.1/32 | Internet loopback | ISP |

#### Private (LAN) Networks - Delft

**STAFFNET VLANs:**

| VLAN ID | Function | Network | Gateway | IPv6 Subnet | IPv6 Gateway |
|---------|----------|---------|---------|-------------|--------------|
| 110 | Management & Administration | 10.10.10.0/24 | 10.10.10.1 | 2001:f187:0:1010::/64 | 2001:f187:0:1010::1 |
| 120 | Marketing & Sales | 10.10.20.0/24 | 10.10.20.1 | 2001:f187:0:1020::/64 | 2001:f187:0:1020::1 |
| 130 | Support | 10.10.30.0/24 | 10.10.30.1 | 2001:f187:0:1030::/64 | 2001:f187:0:1030::1 |
| 140 | IT Services | 10.10.40.0/24 | 10.10.40.1 | 2001:f187:0:1040::/64 | 2001:f187:0:1040::1 |
| 150 | Application Development | 10.10.50.0/24 | 10.10.50.1 | 2001:f187:0:1050::/64 | 2001:f187:0:1050::1 |
| 160 | Meteo Expertise | 10.10.60.0/24 | 10.10.60.1 | 2001:f187:0:1060::/64 | 2001:f187:0:1060::1 |

**CALCNET VLANs:**

| VLAN ID | Function | Network | Gateway | IPv6 Subnet | IPv6 Gateway |
|---------|----------|---------|---------|-------------|--------------|
| 170 | Compute Cluster | 10.10.70.0/24 | 10.10.70.1 | 2001:f187:0:1070::/64 | 2001:f187:0:1070::1 |

**INTRANET VLANs:**

| VLAN ID | Function | Network | Gateway | IPv6 Subnet | IPv6 Gateway |
|---------|----------|---------|---------|-------------|--------------|
| 180 | Internal Services | 10.10.80.0/24 | 10.10.80.1 | 2001:f187:0:1080::/64 | 2001:f187:0:1080::1 |

**DMZNET VLANs:**

| VLAN ID | Function | Network | Gateway | IPv6 Subnet | IPv6 Gateway |
|---------|----------|---------|---------|-------------|--------------|
| 190 | DMZ Services | 10.10.90.0/24 | 10.10.90.1 | 2001:f187:0:1090::/64 | 2001:f187:0:1090::1 |
| 191 | Public DMZ | 10.10.91.0/24 | 10.10.91.1 | 2001:f187:0:1091::/64 | 2001:f187:0:1091::1 |
| 192 | DMZ Management | 10.10.92.0/24 | 10.10.92.1 | 2001:f187:0:1092::/64 | 2001:f187:0:1092::1 |

**Infrastructure Networks:**

| Network | Usage |
|---------|-------|
| 192.168.0.0/30 | Router 1 ↔ C-MLS 1 (192.168.0.1-2) |
| 192.168.0.4/30 | Router 2 ↔ C-MLS 1 (192.168.0.5-6) |
| 192.168.0.8/30 | Router 1 ↔ C-MLS 2 (192.168.0.9-10) |
| 192.168.0.12/30 | Router 2 ↔ C-MLS 2 (192.168.0.13-14) |
| 192.168.0.16/30 | Router 1 ↔ Router 2 (192.168.0.17-18) |
| 192.168.0.20/30 | C-MLS 1 ↔ C-MLS 2 (192.168.0.21-22) |

#### Private (LAN) Networks - Aruba

**STAFFNET VLANs:**

| VLAN ID | Function | Network | Gateway | IPv6 Subnet | IPv6 Gateway |
|---------|----------|---------|---------|-------------|--------------|
| 210 | Marketing & Sales | 10.20.10.0/24 | 10.20.10.1 | 2001:f187:0:2010::/64 | 2001:f187:0:2010::1 |

#### Private (LAN) Networks - Melbourne

**STAFFNET VLANs:**

| VLAN ID | Function | Network | Gateway | IPv6 Subnet | IPv6 Gateway |
|---------|----------|---------|---------|-------------|--------------|
| 310 | Marketing & Sales | 10.30.10.0/24 | 10.30.10.1 | 2001:f187:0:3010::/64 | 2001:f187:0:3010::1 |

#### VPN Tunnel Networks

| Tunnel | Location 1 | Location 2 | IPv6 Subnet |
|--------|-----------|-----------|-------------|
| Aruba ↔ Delft1 | 172.16.0.2/30 | 172.16.0.1/30 | 2001:f187:ffff:1::/64 |
| Aruba ↔ Delft2 | 172.16.0.6/30 | 172.16.0.5/30 | 2001:f187:ffff:2::/64 |
| Melbourne ↔ Delft1 | 172.16.0.10/30 | 172.16.0.9/30 | 2001:f187:ffff:3::/64 |
| Melbourne ↔ Delft2 | 172.16.0.14/30 | 172.16.0.13/30 | 2001:f187:ffff:4::/64 |

### Loopback Addresses

#### Delft Site

| Device | IPv4 Loopback | IPv6 Loopback |
|--------|---------------|---------------|
| Router 1 | 192.168.255.0/32 | 2001:f187:0:1::0/128 |
| Router 2 | 192.168.255.1/32 | 2001:f187:0:1::1/128 |
| C-MLS 1 | 192.168.255.50/32 | 2001:f187:0:1::50/128 |
| C-MLS 2 | 192.168.255.51/32 | 2001:f187:0:1::51/128 |
| ALS 1 | 192.168.255.100/32 | 2001:f187:0:1::100/128 |
| ALS 2 | 192.168.255.101/32 | 2001:f187:0:1::101/128 |
| ALS 3 | 192.168.255.102/32 | 2001:f187:0:1::102/128 |
| ALS 4 | 192.168.255.103/32 | 2001:f187:0:1::103/128 |
| ALS 5 | 192.168.255.104/32 | 2001:f187:0:1::104/128 |
| ALS 6 | 192.168.255.105/32 | 2001:f187:0:1::105/128 |

#### Aruba Site

| Device | IPv4 Loopback | IPv6 Loopback |
|--------|---------------|---------------|
| Router 3 | 192.168.254.0/32 | 2001:f187:1:1::0/128 |
| Switch | 192.168.254.100/32 | 2001:f187:1:1::1/128 |

#### Melbourne Site

| Device | IPv4 Loopback | IPv6 Loopback |
|--------|---------------|---------------|
| Router 4 | 192.168.253.0/32 | 2001:f187:2:1::0/128 |
| Switch | 192.168.253.100/32 | 2001:f187:2:1::1/128 |

### IPv6 Networks

- **Primary Prefix:** 2001:f187::/32
- **Delft Site Prefixes:** 2001:f187:0:1xxx::/64 (where xxx = 010-1092)
- **Aruba Site Prefix:** 2001:f187:0:2010::/64
- **Melbourne Site Prefix:** 2001:f187:0:3010::/64
- **VPN Tunnel Prefixes:** 2001:f187:ffff:x::/64 (where x = 1-4)
- **ISP Loopback:** 2001:db8:ffff::1/128
- **Link-local:** fe80::/10 for all WAN links

## VLANs Summary

### Delft Site

**STAFFNET (110-160):**
- VLAN 110: Management & Administration
- VLAN 120: Marketing & Sales
- VLAN 130: Support
- VLAN 140: IT Services
- VLAN 150: Application Development
- VLAN 160: Meteo Expertise

**CALCNET (170):**
- VLAN 170: Compute Cluster

**INTRANET (180):**
- VLAN 180: Internal Services (NTP, DNS, DHCP, & co)

**DMZNET (190-192):**
- VLAN 190: DMZ Services
- VLAN 191: Public DMZ
- VLAN 192: DMZ Management

**Management:**
- VLAN 999: BLACKHOLE (unused ports)

### Aruba Site

**STAFFNET:**
- VLAN 210: Marketing & Sales

**Management:**
- VLAN 999: BLACKHOLE (unused ports)

### Melbourne Site

**STAFFNET:**
- VLAN 310: Marketing & Sales

**Management:**
- VLAN 999: BLACKHOLE (unused ports)

## Security Features

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

## Routing Protocols

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

## Usage Instructions

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

## Testing & Verification

### Basic Connectivity Tests

```bash
# Test inter-VLAN routing
ping 10.10.20.1 source 10.10.10.1

# Test internet connectivity from Delft
ping 1.1.1.1

# Test site-to-site VPN - Delft to Aruba
ping 10.20.10.1 source 10.10.10.1

# Test site-to-site VPN - Delft to Melbourne
ping 10.30.10.1 source 10.10.10.1

# Verify IPv6 connectivity
ping 2001:db8:ffff::1
ping 2001:f187:0:2010::1
```

### Routing Verification

```bash
# Check IPv4 routing table
show ip route
show ip route bgp
show ip route ospf

# Check IPv6 routing table
show ipv6 route

# Verify OSPF neighbors (Delft site)
show ip ospf neighbor
show ipv6 ospf neighbor

# Check BGP status and neighbors
show ip bgp summary
show ip bgp neighbors
show bgp ipv6 unicast summary

# Verify specific routes
show ip route 10.20.10.0
show ip route 172.16.0.0
```

### Redundancy & High Availability

```bash
# Check HSRP status on C-MLS switches
show standby brief
show standby vlan 110
show standby vlan 120

# Verify tunnel interfaces
show interface tunnel 10
show interface tunnel 11
show interface tunnel 100
show interface tunnel 101

# Check interface status
show ip interface brief
show ipv6 interface brief
```

### Security & Access Control

```bash
# Verify ACLs
show access-lists
show ip access-lists NAT-ACL
show ip access-lists V110_USER_FILTER

# Check NAT translations
show ip nat translations
show ip nat statistics

# Verify SSH configuration
show ip ssh
show crypto key mypubkey rsa

# Check AAA configuration
show aaa servers
show aaa sessions
```

### VPN Tunnel Verification

```bash
# Check GRE tunnel status
show interface tunnel 10
show interface tunnel 11

# Verify tunnel routing
show ip route 172.16.0.0

# Test tunnel connectivity
ping 172.16.0.1 source tunnel 10
ping 172.16.0.5 source tunnel 11

# Check IPsec (if configured)
show crypto ipsec sa
show crypto isakmp sa
```

## Learning Objectives

This comprehensive network topology is designed to teach:

### 1. Enterprise Network Design
- **Hierarchical Design**: Core, distribution, and access layers
- **Multi-site Architecture**: Connecting geographically distributed offices
- **Redundancy Planning**: Eliminating single points of failure
- **Scalability**: Design that accommodates growth

### 2. Advanced Routing
- **BGP Configuration**: Internet connectivity and AS peering
- **OSPF Design**: Single-area and multi-area concepts
- **Route Redistribution**: Between BGP and OSPF
- **Policy-Based Routing**: Traffic engineering and path selection
- **Floating Static Routes**: Backup path configuration

### 3. Layer 3 Switching
- **Inter-VLAN Routing**: Router-on-a-stick vs. L3 switching
- **SVI Configuration**: Switched Virtual Interfaces
- **HSRP Implementation**: Gateway redundancy and failover
- **EtherChannel**: Link aggregation (if implemented)

### 4. Network Segmentation
- **VLAN Design**: Multiple VLANs for different departments
- **STAFFNET**: Employee networks (110-160, 210, 310)
- **CALCNET**: High-performance computing (170)
- **INTRANET**: Internal services (180)
- **DMZNET**: Demilitarized zones (190-192)
- **Security VLANs**: Blackhole for unused ports (999)

### 5. VPN Technologies
- **GRE Tunnels**: Generic Routing Encapsulation
- **IPsec**: Site-to-site encryption
- **BGP over VPN**: Dynamic routing across tunnels
- **Multi-site VPN**: Hub-and-spoke topology

### 6. Dual-Stack Networking
- **IPv4/IPv6 Coexistence**: Running both protocols simultaneously
- **IPv6 Addressing**: Prefix allocation and subnetting
- **OSPFv3**: IPv6 routing protocol
- **IPv6 BGP**: Dual-stack BGP peering

### 7. Network Services
- **DHCP Server**: Dynamic address allocation
- **DHCP Relay**: Helper addresses for centralized DHCP
- **NTP**: Time synchronization across devices
- **SNMP**: Network monitoring and management
- **DNS**: Name resolution configuration

### 8. Security Implementation
- **AAA**: Local authentication, authorization, accounting
- **SSH Configuration**: Secure remote access
- **Access Control Lists**: Traffic filtering and security policies
- **NAT/PAT**: Address translation for internet access
- **Port Security**: Securing access layer switches

### 9. Troubleshooting Skills
- **Show Commands**: Gathering information
- **Ping/Traceroute**: Connectivity testing
- **Debug**: Real-time troubleshooting
- **Log Analysis**: System logs and syslog

### 10. Best Practices
- **Naming Conventions**: Consistent device and interface naming
- **Documentation**: Maintaining accurate network documentation
- **Change Management**: Structured configuration changes
- **Security Hardening**: Disabling unnecessary services

## Configuration Standards

All devices follow these standards:
- Hostname configured
- Domain lookup disabled
- MOTD banner set
- Console timeout: 60 minutes
- SSH enabled (2048-bit RSA)
- AAA authentication
- Logging synchronous
- SNMP monitoring enabled
- HTTP/HTTPS servers disabled

## Contributing

Contributions are welcome! Here's how you can help:

1. **Fork the repository**
2. **Create a feature branch**
```bash
   git checkout -b feature/improved-config
   ```
3. **Commit your changes**
```bash
   git commit -m 'Add improved security configuration for Melbourne site'
   ```
4. **Push to the branch**
```bash
   git push origin feature/improved-config
   ```
5. **Open a Pull Request**

### Contribution Guidelines

- Follow Cisco IOS best practices and standards
- Document all configuration changes in commit messages
- Test configurations thoroughly in Packet Tracer
- Update README.md with any topology or addressing changes
- Include comments in configuration files explaining complex setups
- Maintain consistent naming conventions across devices
- Verify configurations don't break existing functionality

### Areas for Improvement

We welcome contributions in these areas:
- Additional VLANs or network segments
- Enhanced security configurations (firewalls, IPS)
- QoS policies for voice/video traffic
- Wireless controller integration
- Advanced BGP policies
- IPv6 transition mechanisms
- Monitoring and logging enhancements
- Automation scripts for deployment

## Troubleshooting

### Common Issues and Solutions

#### Problem: SSH not working after importing configurations

**Symptoms:** Cannot connect via SSH, "Connection refused" error

**Solution:**
```bash
# Regenerate RSA keys
crypto key zeroize rsa
crypto key generate rsa modulus 2048
# Answer 'yes' when prompted
```

#### Problem: BGP neighbors not establishing

**Symptoms:** BGP state stuck in "Idle" or "Active"

**Diagnosis:**
```bash
show ip bgp summary
show ip bgp neighbors
show ip interface brief
```

**Common Causes:**
- Interface down - check `no shutdown` on interfaces
- Wrong neighbor IP address - verify configuration
- Firewall/ACL blocking TCP 179 - check access lists
- AS number mismatch - verify `remote-as` configuration

**Solution:**
```bash
# Verify neighbor configuration
show run | section router bgp
# Check interface status
show ip interface brief
# Test connectivity to neighbor
ping <neighbor-ip>
# Check for ACLs blocking BGP
show access-lists
```

#### Problem: HSRP not working / No failover

**Symptoms:** Only one gateway responding, no automatic failover

**Diagnosis:**
```bash
show standby brief
show standby vlan <vlan-id>
show ip interface brief
```

**Common Causes:**
- Priority not configured correctly
- Preempt not enabled
- Interface tracking not working
- Different HSRP groups on switches

**Solution:**
```bash
# Verify HSRP configuration on both switches
show run interface vlan 110
# Ensure priorities are different
# C-MLS 1 should have priority 110, C-MLS 2 priority 100
# Enable preempt if needed
interface vlan 110
 standby 110 preempt
```

#### Problem: GRE tunnel down or not passing traffic

**Symptoms:** Tunnel interface shows "up/down" or traffic not reaching remote site

**Diagnosis:**
```bash
show interface tunnel 10
show ip route 172.16.0.0
ping <tunnel-destination>
```

**Common Causes:**
- Source/destination unreachable
- Tunnel IP addresses misconfigured
- Routing not configured for tunnel traffic
- MTU issues

**Solution:**
```bash
# Check tunnel configuration
show run interface tunnel 10
# Verify source/destination reachability
ping <tunnel-destination-ip> source <tunnel-source-interface>
# Check routing
show ip route | include 172.16
# Test tunnel connectivity
ping <remote-tunnel-ip> source tunnel 10
```

#### Problem: Inter-VLAN routing not working

**Symptoms:** Cannot ping between VLANs

**Diagnosis:**
```bash
show ip route
show ip interface brief
show vlan brief
show interfaces trunk
```

**Common Causes:**
- SVI not configured or shutdown
- No IP routing enabled
- ACLs blocking traffic
- VLAN not allowed on trunk
- No default gateway configured on end devices

**Solution:**
```bash
# Enable IP routing
ip routing
# Check SVI status
show ip interface brief | include Vlan
# Bring up SVIs if needed
interface vlan 110
 no shutdown
# Verify trunk configuration
show interfaces trunk
# Check ACLs
show ip access-lists
```

#### Problem: DHCP not working

**Symptoms:** Clients not receiving IP addresses

**Diagnosis:**
```bash
show ip dhcp binding
show ip dhcp pool
show ip helper-address
show run | include helper
```

**Common Causes:**
- DHCP server not reachable
- Helper-address not configured
- DHCP pool exhausted
- Interface not trusted for DHCP

**Solution:**
```bash
# Verify DHCP relay configuration
show run interface vlan 110 | include helper
# Check DHCP pool
show ip dhcp pool
# Verify DHCP server reachability
ping 10.10.80.11
# Check for DHCP snooping issues (if enabled)
show ip dhcp snooping
```

#### Problem: High CPU usage or performance issues

**Symptoms:** Slow response, packet drops, high CPU

**Diagnosis:**
```bash
show processes cpu sorted
show memory
show interfaces | include errors
```

**Common Causes:**
- Routing loops
- Broadcast storms
- Spanning tree issues
- Too many debug commands enabled

**Solution:**
```bash
# Check for loops
show ip route
# Verify spanning tree
show spanning-tree
# Disable debug commands
undebug all
# Check for errors
show interfaces | include errors|drops
```

#### Problem: NTP not synchronizing

**Symptoms:** Device time incorrect, not synchronized with NTP server

**Diagnosis:**
```bash
show ntp status
show ntp associations
ping 10.10.80.11
```

**Solution:**
```bash
# Verify NTP server reachability
ping 10.10.80.11
# Check NTP configuration
show run | include ntp
# Manually sync if needed
ntp server 10.10.80.11
# Wait a few minutes and check again
show ntp status
```

### Debug Commands (Use with Caution)

```bash
# BGP debugging
debug ip bgp events
debug ip bgp keepalives

# OSPF debugging
debug ip ospf events
debug ip ospf adj

# HSRP debugging
debug standby events
debug standby packets

# Remember to disable debugging when done!
undebug all
```

## License

This project is for educational purposes.

---

**Authors:** Max Blaauw, Arne Jansonius, Kai Diemel & Ole Spiegelenberg
**Organization:** HHS (The Hague University of Applied Sciences)
**Domain:** hhs.nl