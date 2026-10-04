# Topology Diagrams

This folder contains sanitized topology diagrams and visual documentation for the Secure Enterprise HQ–Branch Network Lab.

The purpose of this folder is to make the lab easier to understand visually by showing how the HQ, Branch, firewall, switching, VLAN, VPN, and external service components connect together.

---

## 1. Planned Diagrams

The following diagrams will be added as the lab documentation progresses:

| Diagram | Purpose | Status |
|---|---|---|
| Full Enterprise HQ–Branch Topology | Shows complete HQ and Branch network design | Planned |
| HQ Firewall HA Design | Shows FortiGate HA pair, heartbeat, WAN and internal trunking | Planned |
| HQ Switching Design | Shows core/access switching and redundant uplinks | Planned |
| Branch Network Design | Shows Branch firewall, core, distribution and access layer | Planned |
| VLAN Segmentation Overview | Shows VLANs, subnets and gateway placement | Planned |
| IPsec VPN Flow | Shows secure tunnel path between HQ and Branch | Planned |
| AD/DHCP Integration Flow | Shows external VMware server connectivity with HQ VLANs | Planned |
| Troubleshooting Diagrams | Shows before/after flows for troubleshooting cases | Planned |

---

## 2. Diagram Naming Standard

Use clear file names for topology images.

Recommended naming format:

```text
full-enterprise-topology.png
hq-firewall-ha-design.png
hq-switching-design.png
branch-network-design.png
vlan-segmentation-overview.png
ipsec-vpn-flow.png
ad-dhcp-integration-flow.png

3. Current Topology Summary
At a high level, the lab contains:
External WAN / Transport Network
        ↓
HQ FortiGate HA Cluster
        ↓
HQ Core Switching Layer
        ↓
HQ Access Switching Layer
        ↓
HQ Users / Servers / Test Endpoints

Branch connectivity:
Branch Users / Services
        ↓
Branch Access Switching
        ↓
Branch Distribution/Core Layer
        ↓
Branch FortiGate
        ↓
Site-to-Site IPsec VPN
        ↓
HQ FortiGate HA Cluster

External service integration:
VMware Workstation
        ↓
Windows Server 2022
        ↓
Active Directory / DNS / DHCP
        ↓
HQ VLAN 20 Client Testing

4. Diagram Guidelines
All diagrams should be sanitized before upload.
Do not include:
- Real production IP addresses
- Passwords or credentials
- Organization-sensitive names
- Public IP addresses that should remain private
- Confidential topology details
Allowed content:
- Lab IP addresses
- Lab device names
- VLAN IDs
- Interface names
- General traffic flow
- Sanitized troubleshooting paths
5. Recommended Diagram Style
For a professional portfolio, diagrams should be:
- Clean and readable
- High contrast
- Not overcrowded
- Labeled with device names and roles
- Grouped by site: HQ, Branch, External Services
- Showing traffic flow arrows where needed
- Exported as PNG for GitHub display
6. Next Action
The first diagram to add should be:
full-enterprise-topology.png

