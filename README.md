# Secure Enterprise HQ-Branch Network Lab

A practical enterprise network lab designed and implemented in EVE-NG to demonstrate secure connectivity between a headquarters and branch environment.

This project focuses on enterprise switching, FortiGate firewall high availability, VLAN segmentation, routed infrastructure, site-to-site IPsec VPN, Active Directory/DHCP integration, firewall security policies, NAT, failover behavior, and structured troubleshooting documentation.

> Project Status: Active Development  
> Current implementation is being documented and verified phase by phase.

---

## Project Objectives

- Design a realistic enterprise HQ and Branch network
- Implement VLAN-based segmentation
- Configure secure HQ and Branch connectivity
- Deploy FortiGate firewall High Availability
- Configure site-to-site IPsec VPN
- Build redundant switching paths
- Integrate external VMware-based AD/DHCP services
- Validate routing, security policies, NAT, and failover behavior
- Document troubleshooting cases with root cause and lessons learned

---

## Current Architecture

## Headquarters

- Two FortiGate firewalls configured as an HA pair
- Two core switches
- Two access switches with redundant uplinks
- Multiple departmental VLANs
- External VMware-based Active Directory/DHCP server
- Internal test endpoints
- Firewall security zones
- NAT and security policies

## Branch

- One FortiGate firewall
- One core switch
- Two distribution switches
- Two access switches with redundant uplinks
- Multiple user and service VLANs
- Branch test endpoints
- Secure connectivity to headquarters through IPsec VPN

---

## Technologies Used

- EVE-NG
- FortiGate Firewall
- Cisco IOS Switching
- VLANs and IEEE 802.1Q Trunking
- Rapid-PVST
- Static and Inter-VLAN Routing
- FortiGate High Availability
- Site-to-Site IPsec VPN
- Active Directory
- DHCP and DNS
- VMware Workstation
- Firewall Security Policies
- Network Address Translation
- Network Troubleshooting

---

## Repository Structure

```text
secure-enterprise-hq-branch-network/
│
├── architecture/
│   ├── device-inventory.md
│   └── network-overview.md
│
├── topology/
│   └── README.md
│
├── configs/
│   └── README.md
│
├── implementation/
│   └── README.md
│
├── screenshots/
│   └── README.md
│
├── testing/
│   └── README.md
│
├── troubleshooting/
│   └── fortigate-redundant-interface-active-member-issue.md
│
└── README.md

Documentation
Architecture
- [Network Architecture Overview](architecture/network-overview.md)
- [Device Inventory](architecture/device-inventory.md)
Troubleshooting Case Studies
- [FortiGate Redundant Interface Active Member Causing Gateway Reachability Failure](troubleshooting/fortigate-redundant-interface-active-member-issue.md)
Planned Documentation
- VLAN segmentation plan
- FortiGate HA configuration notes
- IPsec VPN implementation notes
- Firewall policy and NAT validation
- HQ-to-Branch connectivity testing
- Failover testing evidence
- Sanitized topology diagrams
- Configuration snippets
Implementation Status
Component	Status
HQ and Branch Architecture	Implemented
HQ FortiGate HA	Implemented
VLAN and Trunk Configuration	Implemented — Verification in Progress
Redundant Switch Connectivity	Implemented — Failover Testing Pending
Branch Routed Infrastructure	Implemented
Site-to-Site IPsec VPN	Implemented — Evidence Collection in Progress
External AD/DHCP Connectivity	Implemented — Documentation in Progress
Security Policy Validation	In Progress
Final Failover Testing	Planned
Monitoring and Hardening	Planned
Final Documentation and Demo	Planned


What This Lab Demonstrates
This lab demonstrates practical understanding of:
- Enterprise network design
- Firewall deployment
- High availability concepts
- VLAN segmentation
- Trunking and Layer 2 troubleshooting
- Site-to-site VPN design
- Firewall policy and NAT behavior
- HQ-to-Branch connectivity
- Real troubleshooting methodology
- Documentation for professional portfolio presentation
Security Notice
This project was created in an authorized virtual lab environment for learning, testing, and professional demonstration.
All addresses, configurations, and credentials are lab-specific. No production credentials or confidential organizational information are included.


Author

Nauman Ali
Network | Security Engineer

[LinkedIn Profile](https://www.linkedin.com/in/nauman-ali-network/) | [GitHub Profile](https://github.com/cdnoman)
