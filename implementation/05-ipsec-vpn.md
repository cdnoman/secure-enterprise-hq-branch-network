# IPsec VPN Implementation

## 1. Objective

The objective of this section is to document the site-to-site IPsec VPN implementation used in the Secure Enterprise HQ–Branch Network Lab.

The VPN connects the Headquarters and Branch environments securely over the WAN/transport network.

This allows users and services at the Branch site to communicate with Headquarters networks through an encrypted tunnel.

---

## 2. Design Summary

The lab uses a route-based IPsec VPN between the HQ FortiGate HA cluster and the Branch FortiGate firewall.

The VPN is designed to provide secure connectivity between:

- HQ internal VLANs
- Branch user/service VLANs
- HQ infrastructure services
- Branch endpoints
- Test systems

The tunnel is used to simulate real enterprise branch-to-headquarters connectivity.

---

## 3. Devices Involved

| Device | Role |
|---|---|
| HQ-FW-PRIMARY / SECONDARY | HQ FortiGate HA cluster and VPN peer |
| BR-FW1 | Branch FortiGate firewall and VPN peer |
| HQ Core Switches | HQ internal VLAN transport |
| Branch Core/Distribution Switches | Branch internal routing and VLAN transport |
| HQ Test Endpoints | HQ-side testing |
| Branch Test Endpoints | Branch-side testing |

---

## 4. VPN Topology

High-level VPN path:

```text
Branch Users / Services
        ↓
Branch Switching Layer
        ↓
Branch FortiGate
        ↓
IPsec VPN Tunnel
        ↓
HQ FortiGate HA Cluster
        ↓
HQ Internal VLANs / Services

The IPsec tunnel name used in the lab:
HQ-TO-BRANCH

5. VPN Type
The VPN design is:
VPN Type: Site-to-Site IPsec VPN
Mode: Route-Based VPN

A route-based VPN allows traffic to be routed into the tunnel through static routes or policy routes.
This makes the design easier to scale and troubleshoot compared with purely policy-based VPN behavior.
6. Protected Networks
The protected networks are the internal networks that should communicate over the VPN.
Example protected network categories:
Site	Network Type
Headquarters	HQ VLANs and internal services
Branch	Branch user and service VLANs


The final list of protected subnets should be verified in Phase 2 selectors, routes, and firewall policies.
7. VPN Configuration Components
The VPN implementation includes:
- Phase 1 configuration
- Phase 2 configuration
- Local and remote subnets
- Peer gateway configuration
- Pre-shared key
- Encryption/authentication settings
- Tunnel interface
- Static routes
- Firewall policies
- NAT exemption or NAT control
- End-to-end traffic testing
Sensitive values such as pre-shared keys and real peer information must not be published.
8. Phase 1 Concept
Phase 1 establishes the secure relationship between the two VPN peers.
It includes:
- Remote gateway / peer IP
- Interface
- Authentication method
- Pre-shared key
- IKE version
- Encryption
- Authentication
- Diffie-Hellman group
- Lifetime
Sanitized conceptual example:
Phase 1 Name: HQ-TO-BRANCH
Remote Gateway: <REMOTE-PEER-IP>
Interface: <WAN-INTERFACE>
Authentication: Pre-shared key
IKE Version: <IKE-VERSION>
Encryption/Auth: <ENCRYPTION-AUTH>

9. Phase 2 Concept
Phase 2 defines which traffic is protected inside the VPN tunnel.
It includes:
- Local subnet
- Remote subnet
- Encryption
- Authentication
- Perfect Forward Secrecy if used
- Lifetime
- Traffic selectors
Conceptual example:
Local subnet: <HQ-SUBNET>
Remote subnet: <BRANCH-SUBNET>
Action: Encrypt traffic over IPsec tunnel

Important note:
Phase 1/Phase 2 up does not automatically prove user traffic is passing.
Routing, policies, NAT and return path must also be verified.

10. Routing Design
Routes are required so that HQ and Branch traffic enters the VPN tunnel.
Example concept:
HQ route to Branch subnet
        ↓
Next-hop / interface: IPsec tunnel

Branch route to HQ subnet
        ↓
Next-hop / interface: IPsec tunnel

Route verification is critical because a tunnel can be up while traffic still does not pass.
11. Firewall Policy Design
Firewall policies must allow traffic between internal zones and the VPN tunnel.
Required policy directions:
HQ LAN → VPN Tunnel
VPN Tunnel → HQ LAN

Branch LAN → VPN Tunnel
VPN Tunnel → Branch LAN

Policies should be verified using:
- Source zone
- Destination zone
- Source subnet
- Destination subnet
- Service/application
- Schedule
- NAT setting
- Policy hit count
- Traffic logs
12. NAT Consideration
For site-to-site VPN traffic, NAT is usually not required between private site subnets unless the design specifically requires it.
Important concept:
Internet traffic usually needs SNAT.
VPN site-to-site traffic usually needs NAT exemption or no NAT.

If VPN traffic is accidentally NATed like internet traffic, the remote side may receive an unexpected source IP and return traffic may fail.
13. Verification Commands
Useful FortiGate VPN checks:
get vpn ipsec tunnel summary
diagnose vpn tunnel list
diagnose vpn ike gateway list
get router info routing-table all
diagnose sys session list
diagnose debug flow
diagnose sniffer packet any "host <source-ip> or host <destination-ip>" 4

Useful traffic tests:
execute ping <remote-ip>
execute traceroute <remote-ip>
ping <remote-host>
traceroute <remote-host>

Useful policy checks:
Check VPN policy hit count
Check traffic logs
Check deny/drop logs
Check route table
Check NAT behavior
Check tunnel counters

14. Validation Tests
Test	Expected Result	Status
Phase 1 status	VPN gateway should be established	Implemented
Phase 2 status	Tunnel selectors should be active	Verification Pending
HQ route to Branch	Route should point toward VPN tunnel	Verification Pending
Branch route to HQ	Route should point toward VPN tunnel	Verification Pending
HQ-to-Branch ping	Should succeed after policy/route validation	Verification Pending
Branch-to-HQ ping	Should succeed after policy/route validation	Verification Pending
Firewall policy hit count	Should increase during VPN traffic	Verification Pending
NAT behavior	VPN traffic should not be incorrectly NATed	Verification Pending
Tunnel counters	Should increase when traffic passes	Verification Pending


15. Issues Faced
During the lab process, IPsec-related traffic required careful validation.
Important areas that needed attention:
- Phase 2 selectors
- Firewall policies
- Static routes
- NAT behavior
- Return path
- Policy deny/default deny behavior
- Protected subnet matching
A VPN tunnel can appear up, but traffic may still fail if route, policy, NAT, or selector configuration is incomplete.
16. Troubleshooting Notes
Common causes of VPN traffic failure:
- Phase 2 selector mismatch
- Missing route to remote subnet
- Missing return route
- Firewall policy missing
- NAT incorrectly applied to VPN traffic
- Remote subnet object mismatch
- Wrong source/destination zone
- Asymmetric routing
- Remote host firewall blocking traffic
Troubleshooting should always confirm:
Is Phase 1 up?
Is Phase 2 up?
Is traffic matching Phase 2 selectors?
Is the route pointing to the tunnel?
Is firewall policy allowing both directions?
Is NAT disabled/exempted for VPN traffic?
Is the remote side routing back correctly?
Are tunnel counters increasing?

17. Current Status
Component	Status
VPN tunnel object	Created
Phase 1 configuration	Implemented
Phase 2 configuration	Verification Pending
Static routes	Verification Pending
Firewall policies	Verification Pending
NAT behavior	Verification Pending
HQ-to-Branch testing	Pending
Branch-to-HQ testing	Pending
Screenshot evidence	Pending


18. One-Line Summary
The HQ-to-Branch IPsec VPN provides secure site-to-site connectivity, but final success depends on validating Phase 2 selectors, routes, firewall policies, NAT behavior, and end-to-end traffic flow.
