# University Campus Network Design and Implementation

This repository contains documentation for the design and implementation of a secure and scalable campus network system for a university. The project encompasses two geographically separated campuses and focuses on establishing a robust, redundant, and secure network infrastructure to support a growing user base and critical services.

The documentation is divided into two main parts:

1.  **Advanced Enterprise Networking Project: Secure Campus Network System Design and Implementation**
2.  **Jeremy's IT Lab CCNA Mega Lab**

---

## 1. Advanced Enterprise Networking Project: Secure Campus Network System Design and Implementation

This section details the architectural design and strategic implementation plan for the university's network. It addresses the needs of two campuses, located 100 miles apart, serving approximately 30,000 users with anticipated growth.

**Key Features and Technologies:**

*   **Dual Campus Architecture:** Supporting a main campus (with a central IT department and server farm) and a branch campus.
*   **Hierarchical Design:** Implemented for scalability, resilience, and ease of management.
*   **Core Infrastructure:**
    *   Cisco ASA 5500-X Firewalls for robust network security, including a Demilitarized Zone (DMZ) for critical servers (DHCP, DNS, FTP, WEB, Email).
    *   Cisco Catalyst 3850 (Distribution/Core) and 2960 (Access) switches.
    *   Redundant server virtualization for essential network services (e.g., DHCP).
    *   Cisco Wireless LAN Controller (WLC) managing Lightweight Access Points (LAPs) for unified wireless access across both campuses.
*   **Security:**
    *   Site-to-site IPsec VPN for secure inter-campus communication.
    *   Security zones and inspection policies on firewalls for granular traffic control.
    *   Standard ACLs for SSH access control.
*   **Redundancy & High Availability:**
    *   Hot Standby Router Protocol (HSRP) for gateway redundancy.
    *   EtherChannel (LACP) for link aggregation and resilience.
    *   Spanning Tree Protocol (STP) with PortFast and BPDUguard for loop prevention and rapid port transitions.
*   **Routing:** OSPF (Open Shortest Path First) as the primary routing protocol across firewalls, routers, and multilayer switches.
*   **Network Services:** DHCP (dynamic IP assignment with helper addresses), DNS, NTP, SNMP, Syslog, FTP, SSH, and NAT.
*   **IP Addressing:** Comprehensive subnetting strategy for LAN, WLAN, Management, DMZ, and public IP ranges.
*   **VLANs:** Dedicated VLANs for Management (10), LAN (20), WLAN (50), and a Blackhole VLAN (199) for unused ports.
*   **Cloud Connectivity:** Integration with Google Cloud Platform for extended service delivery. (Note: not fully implementable on packet tracer)

This part of the documentation includes detailed configuration steps for each technology, from basic device settings to advanced firewall policies and VPN setup.

---

## 2. Jeremy's IT Lab CCNA Mega Lab

This section provides a comprehensive practical lab designed to cover all configuration topics for the CCNA exam. It's an excellent resource for hands-on learning and reinforcing CCNA concepts.

**Lab Scope:**

The lab is structured into nine parts, systematically addressing various networking domains:

*   **Part 1: Initial Setup** - Basic device configurations (hostname, passwords, user accounts, console settings).
*   **Part 2: VLANs, Layer-2 EtherChannel** - VLAN creation, VTP, trunking, native VLANs, DTP, and L2 EtherChannel with LACP/PAGP.
*   **Part 3: IP Addresses, Layer-3 EtherChannel, HSRP** - IP addressing on routers/switches, Layer-3 EtherChannel, and HSRPv2 for gateway redundancy.
*   **Part 4: Rapid Spanning Tree Protocol** - RPSTP configuration, Root Bridge election, PortFast, and BPDU Guard.
*   **Part 5: Static and Dynamic Routing** - OSPF configuration, passive interfaces, network types, and static/floating default routes with redistribution.
*   **Part 6: Network Services: DHCP, DNS, NTP, SNMP, Syslog, FTP, SSH, NAT** - DHCP server/relay, DNS setup, NTP client/server with authentication, SNMP, Syslog, FTP for IOS management, SSH for secure remote access (with ACLs), and Static/Dynamic PAT.
*   **Part 7: Security: ACLs and Layer-2 Security Features** - Extended ACLs for traffic filtering, Port Security, DHCP Snooping, and Dynamic ARP Inspection (DAI).
*   **Part 8: IPv6** - IPv6 routing, interface configuration (EUI-64), and static IPv6 routes.
*   **Part 9: Wireless** - WLC GUI configuration for dynamic interfaces and WLANs (WPA2-PSK).

Each part provides clear, step-by-step instructions for configuring devices in a Packet Tracer environment, offering practical experience with a wide array of CCNA-level technologies.

---

## Repository Structure

The repository contains markdown files (`.md`) detailing the configurations and requirements for both projects.

---

## Sources

*   Jeremy's IT Lab YouTube Channel: [http://www.youtube.com/@JeremysITLab](http://www.youtube.com/@JeremysITLab)
*   [http://www.youtube.com/@gurutechnetworks](http://www.youtube.com/@gurutechnetworks) (Implied source from the second document's structure for the advanced project)

---

This repository serves as a valuable resource for students, network engineers, and anyone looking to understand or implement complex enterprise network designs and practice CCNA-level configurations.
