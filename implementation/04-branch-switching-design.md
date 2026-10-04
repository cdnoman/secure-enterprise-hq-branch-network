# Branch Switching Design Implementation

## 1. Objective

The objective of this section is to document the Branch switching design used in the Secure Enterprise HQ–Branch Network Lab.

The Branch network is designed to simulate a remote office environment with a firewall edge, core switching, distribution switching, access switching, VLAN-based segmentation, and gateway redundancy using HSRP.

This design supports branch users, services, redundant internal paths, and secure connectivity back to Headquarters through site-to-site IPsec VPN.

---

## 2. Design Summary

The Branch site includes:

- One FortiGate firewall
- One branch core switch
- Two distribution switches
- Two access switches
- Redundant distribution-to-access links
- VLAN-based branch segmentation
- HSRP gateway redundancy
- Routed links between firewall, core, and distribution layer
- Test endpoints for connectivity validation

The Branch firewall connects the site to the WAN/IPsec VPN, while the switching layer provides internal connectivity for branch users and services.

---

## 3. Devices Involved

| Device | Role |
|---|---|
| BR-FW1 | Branch FortiGate firewall and IPsec VPN peer |
| BR-CORE-SW1 | Branch core switch connecting firewall and distribution layer |
| BR-DIST-SW1 | Branch distribution switch and HSRP participant |
| BR-DIST-SW2 | Redundant branch distribution switch and HSRP participant |
| BR-ACCESS-SW1 | Branch access switch for endpoints |
| BR-ACCESS-SW2 | Additional branch access switch with redundant uplinks |
| BR-TEST-PC1 | Branch test endpoint for VLAN/routing/VPN validation |

---

## 4. Branch High-Level Topology

The high-level Branch switching design is:

```text
Branch FortiGate
       ↓
Branch Core Switch
       ↓
Branch Distribution Switches
       ↓
Branch Access Switches
       ↓
Branch Users / Services

Redundant access-layer concept:
BR-ACCESS-SW1
   ├── Uplink to BR-DIST-SW1
   └── Uplink to BR-DIST-SW2

BR-ACCESS-SW2
   ├── Uplink to BR-DIST-SW1
   └── Uplink to BR-DIST-SW2

This provides redundant physical paths while STP controls loop prevention at Layer 2.
5. Branch Addressing Summary
The Branch uses the following general addressing summary:
Branch summary: 10.20.0.0/16

Routed /30 transit networks are used between:
- Branch FortiGate and Branch Core Switch
- Branch Core Switch and Distribution Layer
- Distribution switches where required
This makes routing cleaner and helps separate routed infrastructure links from user/service VLANs.
6. Branch VLAN and Gateway Design
The Branch site uses VLANs for user and service separation.
Example VLAN categories:
VLAN Category	Purpose
Management	Network device management
Users	Branch user endpoints
Servers/Services	Branch-local services if required
Voice	Voice endpoints
Guest	Guest access
Infrastructure	Internal infrastructure services
Security	Security tools or monitoring
Quarantine	Isolated devices
Blackhole	Unused or disabled ports


The exact VLAN IDs and subnets should be documented as the branch implementation is finalized.
7. HSRP Gateway Redundancy
The Branch distribution switches participate in HSRP to provide gateway redundancy for branch VLANs.
HSRP allows two Layer 3 switches to share a virtual gateway IP.
Expected behavior:
BR-DIST-SW1
      ↓
Active HSRP gateway

BR-DIST-SW2
      ↓
Standby HSRP gateway

If the active distribution switch fails, the standby switch can take over the virtual gateway role.
8. HSRP Concept
For a branch VLAN, users point to a virtual gateway IP instead of a physical switch IP.
Example concept:
BR-DIST-SW1 VLAN Interface: 10.20.20.2
BR-DIST-SW2 VLAN Interface: 10.20.20.3
HSRP Virtual Gateway:       10.20.20.1

User default gateway:
10.20.20.1

This allows gateway continuity even if one distribution switch becomes unavailable.
9. Routed Infrastructure Links
The Branch design uses routed point-to-point links for infrastructure connectivity.
Benefits:
- Cleaner routing
- Smaller failure domains
- Easier troubleshooting
- Better separation between user VLANs and infrastructure links
- Clear next-hop design
Example routed path:
Branch FortiGate
      ↓
/30 Transit Link
      ↓
Branch Core Switch
      ↓
/30 Transit Links
      ↓
Distribution Switches

10. Layer 2 Redundancy
The access switches are dual-connected toward the distribution layer.
Because redundant Layer 2 links can create loops, STP is required.
Expected behavior:
One uplink: Forwarding
Other uplink: Blocking / Alternate

This keeps the network loop-free while maintaining a backup path.
11. Configuration Summary
Branch switching configuration includes:
- VLAN creation
- Access port assignment
- Trunk links
- STP configuration
- HSRP configuration
- Routed transit interfaces
- Default/static routing
- Endpoint VLAN testing
- Branch-to-HQ connectivity testing
Conceptual access port:
interface EthernetX/X
 description Branch User Endpoint
 switchport mode access
 switchport access vlan <USER-VLAN>
 spanning-tree portfast

Conceptual trunk port:
interface EthernetX/X
 description Branch Access/Distribution Uplink
 switchport mode trunk
 switchport trunk allowed vlan <REQUIRED-VLANS>

Conceptual HSRP interface:
interface Vlan<USER-VLAN>
 ip address <DIST-SW-IP> <SUBNET-MASK>
 standby <GROUP-ID> ip <VIRTUAL-GATEWAY-IP>
 standby <GROUP-ID> priority <PRIORITY>
 standby <GROUP-ID> preempt

12. Verification Commands
Useful Branch switching commands:
show vlan brief
show interfaces trunk
show interfaces status
show spanning-tree vlan <vlan-id>
show mac address-table
show ip interface brief
show ip route
show standby brief
show standby vlan <vlan-id>
show cdp neighbors
show lldp neighbors
show interface counters

Useful endpoint tests:
ping <default-gateway>
ping <branch-core-ip>
ping <branch-firewall-ip>
ping <hq-destination-ip>
traceroute <hq-destination-ip>

13. Validation Tests
Test	Expected Result	Status
Branch VLAN creation	VLANs should exist on required switches	In Progress
Access port assignment	Endpoints should be in correct VLAN	In Progress
Trunk verification	Required VLANs should pass across trunks	In Progress
STP validation	Redundant paths should be controlled by STP	In Progress
HSRP status	One distribution switch active, one standby	Implemented
HSRP preempt	Preferred switch should reclaim active role	Implemented
Routed transit links	Branch routing should be stable	Implemented
Branch-to-firewall reachability	Branch core should reach firewall	Verified
Branch-to-HQ VPN traffic	Branch should reach HQ over IPsec	Verification Pending


14. Issues Faced
During Branch implementation, some key items required attention:
- HSRP preempt behavior needed verification
- Redundant access paths required STP validation
- Branch routing needed careful next-hop planning
- IPsec VPN traffic required firewall route/policy verification
- Branch-to-HQ reachability needed end-to-end testing
These items will be documented further in troubleshooting and testing files as evidence is collected.
15. Troubleshooting Notes
Important troubleshooting points for the Branch switching layer:
- HSRP active/standby status must be verified
- The virtual gateway should be reachable from endpoints
- STP state must be checked per VLAN
- Trunk allowed VLANs must be reviewed carefully
- Routed /30 links should be tested hop by hop
- Branch firewall reachability should be verified before VPN testing
- Route tables should be checked on core, distribution, and firewall devices
- Endpoint testing should include gateway, branch firewall, and HQ destination reachability
16. Current Status
Component	Status
Branch firewall connectivity	Implemented
Branch core switch	Implemented
Branch distribution switches	Implemented
Branch access switches	Implemented
HSRP gateway redundancy	Implemented
HSRP preempt behavior	Configured
Branch routing	Implemented
Branch STP validation	In Progress
Branch-to-HQ VPN testing	Verification Pending
Final screenshots	Pending


17. One-Line Summary
The Branch switching design provides a realistic remote-site network with routed infrastructure links, VLAN-based access connectivity, HSRP gateway redundancy, and secure HQ connectivity through IPsec VPN.
