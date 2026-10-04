# VLAN Segmentation Implementation

## 1. Objective

The objective of this section is to document the VLAN segmentation design used in the Secure Enterprise HQ–Branch Network Lab.

VLAN segmentation is used to separate different types of traffic, improve security, simplify troubleshooting, and allow firewall-based control between network segments.

In this lab, the FortiGate firewall provides the default gateways for HQ VLANs, while Cisco switching is used to carry VLAN traffic across trunk links.

---

## 2. Design Summary

The Headquarters network uses multiple VLANs for logical separation of traffic.

Each VLAN represents a different department, service type, or security zone.

The FortiGate firewall acts as the default gateway for the routed VLANs and controls inter-VLAN communication through firewall policies.

The HQ switching layer carries VLAN-tagged traffic between:

- FortiGate firewall
- Core switches
- Access switches
- Endpoints
- External VMware-based services

---

## 3. HQ VLAN Plan

| VLAN | Name | Subnet | Gateway |
|---:|---|---|---|
| 10 | Management | 10.10.10.0/24 | 10.10.10.1 |
| 20 | Users | 10.10.20.0/24 | 10.10.20.1 |
| 30 | Voice | 10.10.30.0/24 | 10.10.30.1 |
| 40 | Servers | 10.10.40.0/24 | 10.10.40.1 |
| 50 | Infrastructure | 10.10.50.0/24 | 10.10.50.1 |
| 60 | Storage | 10.10.60.0/24 | 10.10.60.1 |
| 70 | Backup | 10.10.70.0/24 | 10.10.70.1 |
| 80 | Guest | 10.10.80.0/24 | 10.10.80.1 |
| 90 | DMZ | 10.10.90.0/24 | 10.10.90.1 |
| 100 | Security | 10.10.100.0/24 | 10.10.100.1 |
| 110 | Corporate Wi-Fi | 10.10.110.0/24 | 10.10.110.1 |
| 120 | IoT | 10.10.120.0/24 | 10.10.120.1 |
| 130 | Quarantine | 10.10.130.0/24 | 10.10.130.1 |
| 999 | Blackhole | No routed gateway | Not applicable |

---

## 4. Gateway Placement

The FortiGate firewall provides the default gateway for the HQ routed VLANs.

Example:

```text
VLAN 20 Users
Subnet: 10.10.20.0/24
Gateway: 10.10.20.1
Gateway location: FortiGate VLAN interface

This design allows the firewall to control traffic between VLANs.
For example:
Users VLAN → Servers VLAN
Users VLAN → Internet
Guest VLAN → Internet only
IoT VLAN → Restricted access
DMZ VLAN → Controlled inbound/outbound access

5. Trunking Design
Because the FortiGate VM image has limited physical interfaces, VLAN trunking is used.
The FortiGate internal interface carries multiple VLANs using IEEE 802.1Q tagging.
HQ FortiGate
Interface	Purpose
port1	WAN, management access and IPsec transport
port2	802.1Q trunk carrying HQ VLANs
port3	Dedicated HA heartbeat
port4	Reserved


Trunk Path
FortiGate port2
      ↓
HQ Core Switch
      ↓
HQ Access Switches
      ↓
End Devices

6. Switching Design
The HQ switching layer carries VLANs across trunk links.
The design includes:
- Core switch layer
- Access switch layer
- Redundant uplinks
- Rapid-PVST loop prevention
- VLAN trunking
- Access ports for endpoint connectivity
Access ports are assigned to the required VLAN based on endpoint type.
Example:
User PC → VLAN 20
Server endpoint → VLAN 40
Management endpoint → VLAN 10
Guest endpoint → VLAN 80

7. Example Access Port Concept
Example access port logic:
interface EthernetX/X
 description User Endpoint
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast

This places the connected endpoint into VLAN 20.
8. Example Trunk Port Concept
Example trunk port logic:
interface EthernetX/X
 description Uplink to Core / Firewall
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,100,110,120,130

This allows multiple VLANs to pass over a single physical link.
9. Blackhole VLAN
VLAN 999 is used as a blackhole or unused VLAN.
Purpose:
- Assign unused ports to a non-routed VLAN
- Reduce risk of accidental network access
- Keep unused ports isolated
- Improve security posture
VLAN 999 does not have a routed gateway.
10. Verification Commands
Useful Cisco switching commands:
show vlan brief
show interfaces trunk
show interfaces status
show spanning-tree vlan <vlan-id>
show mac address-table
show mac address-table vlan <vlan-id>
show ip interface brief

Useful FortiGate checks:
get system interface
show system interface
get router info routing-table all
execute ping <gateway-or-host>
diagnose sniffer packet any "host <ip-address>" 4

11. Validation Tests
The following validation tests should be performed:
Test	Expected Result	Status
VLAN exists on switches	VLAN should appear in VLAN database	Verified
Trunk carries required VLANs	VLANs should be allowed on trunk links	Verified
VLAN gateway reachable	Endpoint should ping its default gateway	Verified
MAC learning working	MAC addresses should appear on expected ports	Verified
STP stable	Redundant paths should be controlled by STP	Verified
DHCP relay for VLAN 20	Client should receive IP from external DHCP server	Verified
Inter-VLAN access	Controlled by firewall policy	In Progress


12. Issues Faced
During VLAN and trunk verification, troubleshooting was required for reachability between the Core switch and FortiGate VLAN gateway.
One key issue identified during lab implementation was related to the FortiGate redundant interface active member selection.
That troubleshooting case is documented separately:
troubleshooting/fortigate-redundant-interface-active-member-issue.md

13. Lessons Learned
- VLAN design should be documented before implementation.
- Gateway placement is important for traffic control.
- Trunk allowed VLAN lists must be verified carefully.
- Interface up does not always mean VLAN traffic is passing.
- STP state should be checked during redundant switching validation.
- FortiGate VLAN interfaces must be bound to the correct physical or logical interface.
- DHCP relay testing confirms that client VLANs can reach infrastructure services.
- Troubleshooting should follow the path from endpoint to gateway.
14. Current Status
Component	Status
HQ VLAN plan	Documented
HQ VLAN gateways	Implemented
FortiGate VLAN interfaces	Implemented
HQ trunking	Verified
HQ access VLANs	Implemented
DHCP relay for VLAN 20	Verified
Inter-VLAN firewall policy validation	In Progress
Final screenshots	Pending


15. One-Line Summary
VLAN segmentation in this lab separates enterprise traffic into controlled network zones, with FortiGate acting as the gateway and policy enforcement point for HQ VLANs.
