# HQ Switching Design Implementation

## 1. Objective

The objective of this section is to document the Headquarters switching design used in the Secure Enterprise HQ–Branch Network Lab.

The HQ switching layer provides VLAN transport, access-layer connectivity, redundant uplinks, trunking, and Layer 2 loop prevention using Rapid-PVST.

This design supports multiple VLANs behind the FortiGate HA cluster and allows endpoints, servers, and external service integrations to communicate through controlled switching paths.

---

## 2. Design Summary

The HQ switching design includes:

- Two core switches
- Two access switches
- Redundant access-to-core uplinks
- VLAN trunking
- Rapid-PVST loop prevention
- FortiGate internal trunk connectivity
- Endpoint access ports
- External VMware client/server connectivity

The FortiGate firewall acts as the default gateway for HQ VLANs, while the Cisco switching layer carries VLAN-tagged traffic between the firewall, core switches, access switches, and endpoints.

---

## 3. Devices Involved

| Device | Role |
|---|---|
| HQ-CORE-SW1 | Primary HQ core switch and Rapid-PVST root |
| HQ-CORE-SW2 | Secondary HQ core switch and redundant switching path |
| HQ-ACCESS-SW1 | Access switch for HQ endpoints and external VMware client connectivity |
| HQ-ACCESS-SW2 | Access switch providing additional access-layer connectivity |
| HQ-FW-PRIMARY / SECONDARY | FortiGate HA cluster providing VLAN gateways and policy enforcement |
| Server1 | External VMware AD/DNS/DHCP server |
| PC2 | External Windows domain client in VLAN 20 |
| HQ-TEST-PC1 | Internal test endpoint used for connectivity validation |

---

## 4. HQ Switching Topology

The high-level HQ switching design is:

```text
FortiGate HA Cluster
        ↓
HQ Core Switching Layer
        ↓
HQ Access Switching Layer
        ↓
Users / Servers / Test Endpoints

Redundant switching path concept:
HQ-ACCESS-SW1
   ├── Uplink to HQ-CORE-SW1
   └── Uplink to HQ-CORE-SW2

HQ-ACCESS-SW2
   ├── Uplink to HQ-CORE-SW1
   └── Uplink to HQ-CORE-SW2

Rapid-PVST controls the redundant Layer 2 paths to prevent loops.
5. VLAN Transport Design
The HQ switching layer carries multiple VLANs across trunk links.
HQ VLANs include:
VLAN	Name	Purpose
10	Management	Network management access
20	Users	User endpoint connectivity
30	Voice	Voice services
40	Servers	Server network
50	Infrastructure	Infrastructure services
60	Storage	Storage traffic
70	Backup	Backup traffic
80	Guest	Guest access
90	DMZ	Demilitarized zone
100	Security	Security tools/services
110	Corporate Wi-Fi	Corporate wireless users
120	IoT	IoT devices
130	Quarantine	Isolated/limited access devices
999	Blackhole	Unused/disabled port isolation


6. Trunk Design
Trunk links are used between:
- FortiGate and HQ core switch
- Core switches and access switches
- Inter-switch links where required
Example trunk concept:
FortiGate port2
        ↓
HQ Core Switch
        ↓
HQ Access Switches

Trunks carry required VLANs using IEEE 802.1Q tagging.
Example conceptual configuration:
interface EthernetX/X
 description Trunk Uplink
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,100,110,120,130

VLAN 999 is used as a blackhole VLAN and is not used as a routed production gateway.
7. Access Port Design
Access ports are assigned based on endpoint type.
Example mapping:
Endpoint Type	VLAN
User PC	VLAN 20
Server endpoint	VLAN 40
Management endpoint	VLAN 10
Guest endpoint	VLAN 80
IoT endpoint	VLAN 120
Unused port	VLAN 999


Example access port concept:
interface EthernetX/X
 description User Endpoint
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast

Unused ports should be placed in VLAN 999 or administratively shut down.
8. Rapid-PVST Design
Rapid-PVST is used to prevent Layer 2 loops while keeping redundant physical paths available.
Verified behavior:
- HQ Core 1 acts as the root bridge for operational VLANs
- Access switches use Core 1 uplinks as forwarding paths
- Core 2 uplinks remain available as STP-controlled standby paths
- Redundant paths are blocked where required by STP
- A previously disabled redundant uplink on HQ Access Switch 2 was identified and restored
Expected STP behavior:
Primary uplink: Forwarding
Redundant uplink: Blocking / Alternate

This provides redundancy while avoiding Layer 2 loops.
9. Root Bridge Design
HQ-CORE-SW1 is used as the preferred root bridge for operational VLANs.
Purpose:
- Predictable Layer 2 forwarding path
- Controlled traffic direction
- Easier troubleshooting
- Stable redundancy design
Conceptual root bridge configuration:
spanning-tree vlan 10,20,30,40,50,60,70,80,90,100,110,120,130 priority 4096

The secondary core switch can be configured with a higher priority value to act as backup root.
Conceptual secondary root configuration:
spanning-tree vlan 10,20,30,40,50,60,70,80,90,100,110,120,130 priority 8192

10. External VMware Connectivity
The lab includes an external VMware-based Windows Server and Windows domain client.
External services:
Device	Role	IP Address
Server1	AD/DNS/DHCP	192.168.75.160/24
PC2	Windows domain client	DHCP: 10.10.20.53/24


The Windows client successfully received its VLAN 20 address through FortiGate DHCP relay.
This validates that VLAN 20 traffic can reach the external DHCP service path.
11. Verification Commands
Useful HQ switching verification commands:
show vlan brief
show interfaces trunk
show interfaces status
show spanning-tree
show spanning-tree vlan <vlan-id>
show spanning-tree interface <interface-id>
show mac address-table
show mac address-table vlan <vlan-id>
show cdp neighbors
show lldp neighbors
show interface counters
show ip interface brief

Useful endpoint tests:
ping <default-gateway>
ping <dhcp-server>
ping <dns-server>
ipconfig /all
ipconfig /release
ipconfig /renew
nslookup <domain-name>

12. Validation Tests
Test	Expected Result	Status
VLAN database check	Required VLANs should exist	Verified
Trunk allowed VLAN check	Required VLANs should be allowed	Verified
Access port VLAN check	Endpoints should be in correct VLAN	Verified
STP state check	Redundant paths should be controlled by STP	Verified
Root bridge check	HQ-CORE-SW1 should be root for operational VLANs	Verified
MAC learning check	MAC addresses should appear on expected interfaces	Verified
VLAN 20 DHCP relay test	PC2 should receive IP from DHCP server	Verified
Gateway reachability test	VLAN endpoints should reach FortiGate gateway	Verified
AD/DNS reachability	Connectivity testing in progress	In Progress


13. Issues Faced
During HQ switching verification, the following issues were identified or investigated:
- Gateway reachability issue between Core switch and FortiGate VLAN gateway
- Redundant interface active member issue on FortiGate
- Disabled redundant uplink on HQ Access Switch 2
- STP forwarding/blocking behavior verification
- VLAN trunk and gateway validation
- Live AD/DNS connectivity still under investigation
The FortiGate redundant interface active member issue is documented separately:
troubleshooting/fortigate-redundant-interface-active-member-issue.md

14. Troubleshooting Notes
Important troubleshooting lessons from the HQ switching layer:
- Interface up does not always mean VLAN traffic is forwarding
- STP blocking is expected in redundant Layer 2 designs
- Trunk allowed VLAN lists must be checked carefully
- MAC address learning helps confirm traffic path
- Gateway reachability should be tested from the switching layer
- FortiGate interface behavior should be verified along with switch trunking
- Redundant paths must be tested, not only configured
- DHCP relay testing is useful for validating VLAN-to-service reachability
15. Current Status
Component	Status
HQ VLANs	Implemented
HQ access switching	Implemented
HQ core switching	Implemented
FortiGate trunk connectivity	Implemented
Rapid-PVST	Verified
Root bridge placement	Verified
Redundant uplinks	Implemented
Disabled uplink issue	Resolved
VLAN 20 DHCP relay	Verified
AD/DNS reachability	Troubleshooting in Progress
Final screenshots	Pending


16. One-Line Summary
The HQ switching design provides VLAN transport, redundant access-to-core connectivity, and loop prevention using Rapid-PVST, while FortiGate acts as the default gateway and policy enforcement point for internal VLANs.
