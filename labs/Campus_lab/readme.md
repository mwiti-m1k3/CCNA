# Advanced Enterprise Networking Project: Secure Campus Network System Design and Implementation

This document outlines the design and implementation of a secure campus network system for a university with two campuses 100 miles apart. The network aims for top-tier performance, redundancy, scalability, and availability.

**Key Infrastructure Components:**

*   **Internet Services Provider (ISP):**.
*   **Network Security:** Two Cisco ASA 5500-X series Firewalls (one per campus, Main Campus hosts DMZ).
*   **Network Routing:** Firewalls and Core Switches will handle routing.
*   **Switching Infrastructure:**
    *   Two Catalyst 3850 48-Port Switches per campus (Distribution/Core).
    *   Catalyst 2960 48-Port Switches per faculty (Access).
*   **Server Hardware and Virtualization:** Two physical servers for virtualization (DHCP, DNS, FTP, WEB, Email), with redundant DHCP.
*   **Wireless Infrastructure:** Cisco Wireless LAN Controller (WLC) at the Main Campus managing Lightweight Access Points (LAPs) across both campuses.
*   **Site-to-site IPsec VPN:** Configured on firewalls for secure inter-campus communication.
*   **Cloud Computing:** Link to Google Cloud Platform for service delivery.

**Network Segmentation:**

*   All LAN and WLAN users will be on separate network segments within the same local area network.
*   Firewalls will define security zones and filter traffic based on inspection policies.
*   Secure IPsec VPN tunnel between campuses.

---

## Requirements

**IP Address Ranges:**

*   **Management Network:** 192.168.10.0/24
*   **WLAN:**
    *   Main Campus: 10.10.0.0/16
    *   Branch Campus: 10.11.0.0/16
*   **LAN:**
    *   Main Campus: 172.16.0.0/16
    *   Branch Campus: 172.17.0.0/16
*   **DMZ:** 10.20.20.0/27
*   **Public Addresses:**
    *   Main Campus: 105.100.50.0/30
    *   Branch Campus: 205.200.100.0/30

**Technical Requirements:**

1.  **Design Tool:** Cisco Packet Tracer.
2.  **Hierarchical Design:** Implement with redundancy.
3.  **ISPs:** Establish connectivity to an Airtel ISP Router.
4.  **WLC:** Each department equipped with WAP, centrally managed by WLC.
5.  **VLANs:**
    *   VLAN 10: Management
    *   VLAN 20: LAN
    *   VLAN 50: WLAN
    *   VLAN 199: Blackhole (unused ports)
6.  **EtherChannel:** Implement Link Aggregation Control Protocol (LACP).
7.  **STP PortFast and BPDUguard:** Configure on all access ports.
8.  **Subnetting:** Allocate IP addresses to each network group.
9.  **Basic Settings:** Hostnames, console passwords, enable passwords, banner messages, password encryption, disable IP domain lookup.
10. **Inter-VLAN Routing:** Enable communication between departments via multilayer switch.
11. **Core Switch IP Addressing:** Assign IP addresses to Multilayer switches.
12. **DHCP Server:** All devices obtain IP addresses dynamically from DHCP servers in the server farm.
13. **HSRP:** Implement for redundancy, load balancing, and failover.
14. **Static Addressing:** Allocate static IP addresses to devices in the server room.
15. **Routing Protocol:** OSPF on firewall, routers, and multilayer switches.
16. **Standard ACL for SSH:** Permit SSH for Senior Network Security Engineer PC on VTY lines.
17. **Cisco ASA Firewall:** Configure default static routes, basic settings, security levels, zones, and policies.
18. **IPsec VPN:** Configure for secure communication between campuses.
19. **Final Testing:** Verify proper communication and functionality.

**Concepts to Implement:**

DHCP, HSRP, Static Addressing, OSPF, Standard ACL, SSH, EtherChannel, Subnetting, VLAN, Trunking and Inter-VLAN Routing, NAT, Spanning Tree Protocol, and Virtualization.

---

## Solution: Configuration Steps

### 1. Network Design and Diagram

1.  Add Equipment in a hierarchical manner.
2.  Add Links considering VLANs, port assignment, bandwidth, reliability, and redundancy.

### 2. Basic Settings to All Devices + SSH + Standard ACL for SSH

**Example: HQ-FWL**

```
!
en
conf t
hostname HQ-FWL
banner motd ^C

** W A R N I N G **
This system is for authorized use only. Unauthorized access or use may result in
criminal prosecution and/or disciplinary action. All activities on this system
are subject to monitoring and recording. By logging into this system, you agree
to comply with all applicable policies and procedures of this organization.

^C
enable secret password
service password-encryption
no ip domain-lookup
spanning-tree mode pvst
ip domain-name mike.com
username mike secret password
crypto key generate rsa
1024
access-list 2 permit 192.168.10.0 255.255.255.0
access-list 2 deny any
line vty 0 15
login local
transport input ssh
access-class 2 in
line con 0
password password
exec-timeout 3 0
logging synchronous
login
do wr
```

### 3. VLAN Assignment plus Access and Trunk Ports on L2 and L3 Switches

**L3 Switches Example: HQ-CSW1**

```
!
en
conf t
vlan 10
 name Management
exit
vlan 20
 name LAN
exit
vlan 50
 name WLAN
exit
int range gig1/0/2-6
 switchport mode trunk
int gig1/0/10
 switchport mode access
 switchport access vlan 50
do wr
```

**Branch L3 Switches Example: BR-CSW1**

```
!
en
conf t
vlan 60
 name BLAN
exit
vlan 90
 name BWLAN
exit
int range gig1/0/2-5
 switchport mode trunk
do wr
```

**L2 Switches Example: HQ-H&S-SW**

```
!
en
conf t
vlan 10
 name Management
exit
vlan 20
 name LAN
exit
vlan 50
 name WLAN
exit
vlan 199
 name Blackhole
exit
int range fa0/1-2
 switchport mode trunk
int range fa0/3-20
 switchport mode access
 switchport access vlan 20
int range fa0/21-24
 switchport mode access
 switchport access vlan 50
int range gig0/1-2
 switchport mode access
 switchport access vlan 199
 shutdown
do wr
```

**L2 Switches Example: HQ-IT-SW** (Note: No Blackhole VLAN 199)

```
!
en
conf t
vlan 10
 name Management
exit
vlan 20
 name LAN
exit
vlan 50
 name WLAN
exit
int range fa0/1-2
 switchport mode trunk
int range fa0/3-20
 switchport mode access
 switchport access vlan 20
int range fa0/21-24
 switchport mode access
 switchport access vlan 50
int range gig0/1-2
 switchport mode access
 switchport access vlan 10
do wr
```

**Branch L2 Switches Example: BR-H&S-SW**

```
!
en
conf t
vlan 60
 name BLAN
exit
vlan 90
 name BWLAN
exit
vlan 199
 name Blackhole
exit
int range fa0/1-2
 switchport mode trunk
int range fa0/3-20
 switchport mode access
 switchport access vlan 60
int range fa0/21-24
 switchport mode access
 switchport access vlan 90
int range gig0/1-2
 switchport mode access
 switchport access vlan 199
do wr
```

### 4. STP Portfast and BPDUguard Configurations on All Access Ports

**Example:**

```
!
en
conf t
int (interface or given interface range)
 spanning-tree portfast
 spanning-tree bpduguard enable
do wr
```

### 5. EtherChannel (Active/Active or Active/Passive)

**Example: HQ-CSW1**

```
!
en
conf t
int (interface or interface range) eg (gig1/0/2 or gig1/0/4-6)
 channel-group 1 mode active
interface port-channel 1
 switchport mode trunk
exit
do wr
```

**Example: HQ-CSW2**

```
!
en
conf t
int (interface or interface range) eg (gig1/0/2 or gig1/0/4-6)
 channel-group 1 mode active
interface port-channel 1
 switchport mode trunk
exit
do wr
show etherchannel port-channel
```

### 6. Subnetting and IP Addressing

**WLAN:**

*   **Main Network:** 10.10.0.0/16 (Valid Hosts: 10.10.0.1 - 10.10.255.254, Default Gateway: 10.10.0.1, Broadcast: 10.10.255.255)
*   **Branch Network:** 10.11.0.0/16 (Valid Hosts: 10.11.0.1 - 10.11.255.254, Default Gateway: 10.11.0.1, Broadcast: 10.11.255.255)

**LAN:**

*   **Main Network:** 172.16.0.0/16 (Valid Hosts: 172.16.0.1 - 172.16.255.255, Default Gateway: 172.16.0.1, Broadcast: 172.16.255.255)
*   **Branch Network:** 172.17.0.0/16 (Valid Hosts: 172.17.0.1 - 172.17.255.255, Default Gateway: 172.17.0.1, Broadcast: 172.17.255.255)

**Management:**

*   **Network:** 192.168.10.0/24 (Valid Hosts: 192.168.10.1 - 192.168.10.254, Default Gateway: 192.169.10.1, Broadcast: 172.16.10.255)

**DMZ:**

*   **Network:** 10.20.20.0/27 (Default Gateway: 10.20.20.1, Broadcast: 10.20.20.31)

**Inter-Device Links:**

*   **CLOUD Area:** 8.0.0.0/8
*   **HQ-ISP Internet:** 20.20.20.0/30
*   **Branch-ISP Internet:** 30.30.30.0/30
*   **HQ-FWL ISP:** 105.100.50.0/30
*   **Branch-FWL ISP:** 205.200.100.0/30
*   **HQ-FWL to -MLSW1:** 10.20.20.32/30
*   **HQ-FWL to -MLSW2:** 10.20.20.36/30
*   **Branch-FWL to -MLSW1:** 10.20.20.40/30
*   **Branch-FWL to -MLSW2:** 10.20.20.44/30

**Enable IP Routing on L3 Switches:**

```
!
en
conf t
ip routing
exit
```

**Assign IP Network Addresses (Example: HQ-CSW1):**

```
!
en
conf t
int gig1/0/1
 no switchport
 ip address 10.20.20.33 255.255.255.252
do wr
exit
```

**Assign IP Network Addresses (Example: HQ-Internet Router):**

```
!
en
conf t
ip routing
int gig0/0
 ip address 105.100.50.1 255.255.255.252
do wr
exit
```

**Assign IP Network Addresses (Example: HQ-FW):**

```
!
en
conf t
int gig1/1
 no shutdown
 nameif INSIDE1
 security-level 100
 ip address 10.20.20.34 255.255.255.252
exit
wr
```

### 7. HSRP and Inter-VLAN Routing on the L3 Switches plus IP DHCP Helper Addresses

**Process:**

1.  **Configure HSRP:** On routed ports or SVIs (Switched Virtual Interfaces).
    *   Example: `interface Vlan1`, `standby 1 ip 192.168.1.254` (192.168.1.254 is virtual gateway, 1 is group number).
2.  **Configure Inter-VLAN Routing:** By creating VLANs and enabling IP routing.
    *   Example: `interface Vlan10`, `ip address 10.1.10.1 255.255.255.0`.
3.  **Configure IP DHCP Helper Addresses:** To forward DHCP broadcasts.
    *   Example: `interface Vlan10`, `ip helper-address 192.168.1.1`.

**Example: HQ-CSW1 (SVI Configuration)**

```
!
en
conf t
int vlan 10
 ip address 192.168.10.2 255.255.255.0
 ip helper-address 10.20.20.5
 ip helper-address 10.20.20.6
 standby 10 ip 192.168.10.1
do wr
```

*(Repeat for all other VLANs)*
`show standby brief` (To check HSRP status)

### 8. Static IP Address to DMZ/Server Farm Devices

Assign IP addresses statically to servers (DHCP1, DHCP2, DNS, WEB, EMAIL, FTP) per the given IP range in the subnetting section.

### 9. DHCP Server Device Configurations

Configure DHCP servers (e.g., BRANCHLANPool) via GUI. Ensure connectivity to the server farm network's default gateway.

### 10. OSPF on the Firewall, Routers, and Switches

**Example: HQ-CSW1**

```
!
en
conf t
router ospf 15
 router-id 2.1.2.1
 network 192.168.10.0 0.0.0.3 area 0
 network 172.16.0.0 0.0.0.255 area 0
 network 10.10.0.0 0.0.255.255 area 0
do wr
exit
```

**Example: HQ-FW**

```
!
en
conf t
router ospf 15
 router-id 2.2.2.2
 network 105.100.50.0 255.255.255.252 area 0
 network 10.20.20.0 255.255.255.224 area 0
 network 10.20.20.32 255.255.255.252 area 0
 network 10.20.20.0 255.255.255.224 area 0
wr mem
```

*(Do the same for the other firewall. Note that network assignment does not include wildcards in FW config.)*

### 11. Firewall Interface Security Zones and Levels

**Example: HQ-FW (Same as in IP Addressing)**

```
!
en
conf t
int gig1/1
 no shutdown
 nameif INSIDE1
 security-level 100
 ip address 10.20.20.34 255.255.255.252
exit
wr
```

### 12. Firewall Inspection Policy Configuration

**Configure FW to route all networks to the ISP (Static Route - Example: HQ-FW):**

```
!
en
conf t
route OUTSIDE 0.0.0.0 0.0.0.0 105.100.50.1
wr mem
```

*(Do the same for BRANCH-FW. `OUTSIDE` is the name defining the interface connected to the ISP.)*

**Create Object Networks (Example: HQ-FW):**

```
!
en
conf t
object network INSIDE1-OUTSIDE
 subnet 192.168.10.0 255.255.255.0
 description LAN network segment
 nat (INSIDE1, OUTSIDE1) dynamic interface
object network INSIDE1a-OUTSIDE
 SUBNET 192.168.10.0 255.255.255.0
 description LAN network segment- for the redundant CSW
 nat (INSIDE2, OUTSIDE1) dynamic interface
exit
wr mem
```

*(Do the same for all other subnets.)*

### 13. Wireless Network Configurations

(Specific configuration steps for WLC and APs would go here, not provided in original summary.)

### 14. IPsec VPN on the Firewalls

(Specific configuration steps for IPsec VPN would go here, not provided in original summary.)

### 15. Verifying and Testing Configurations

Conduct thorough testing to verify proper communication and ensure that all configured elements function as intended.

---

## Sources

*   http://www.youtube.com/@gurutechnetworks
```
