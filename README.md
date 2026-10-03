# Secure Enterprise HQ–Branch Network

A practical enterprise network designed and implemented in EVE-NG to demonstrate secure connectivity between a headquarters and branch environment.

The project combines enterprise switching, FortiGate high availability, VLAN segmentation, routed infrastructure, site-to-site IPsec VPN, Active Directory integration, security policies, redundancy, and structured testing.

> **Project Status:** Active Development  
> Current implementation is being documented and verified phase by phase.

## Project Objectives

- Design a realistic enterprise HQ and branch network.
- Provide secure connectivity between both locations.
- Implement FortiGate high availability at headquarters.
- Segment departments and services using VLANs.
- Build redundant switching paths.
- Integrate external VMware-based Active Directory and DHCP services.
- Configure and test a site-to-site IPsec VPN.
- Validate connectivity, security policies and failover behaviour.
- Document configurations, testing evidence and troubleshooting scenarios.

## Current Architecture

### Headquarters

- Two FortiGate firewalls configured as an HA pair
- Two core switches
- Two access switches with redundant uplinks
- Multiple departmental VLANs
- External VMware-based Active Directory/DHCP server
- External Windows domain client
- Internal test endpoint

### Branch

- One FortiGate firewall
- One core switch
- Two distribution switches
- Two access switches with redundant uplinks
- Multiple user and service VLANs
- Branch test endpoints
- Secure connectivity to headquarters through IPsec VPN

## Technologies

- EVE-NG
- Fortinet FortiGate
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

## Implementation Status

| Component | Status |
|---|---|
| HQ and Branch Architecture | Implemented |
| HQ FortiGate HA | Implemented |
| VLAN and Trunk Configuration | Implemented — Verification in Progress |
| Redundant Switch Connectivity | Implemented — Failover Testing Pending |
| Branch Routed Infrastructure | Implemented |
| Site-to-Site IPsec VPN | Implemented — Evidence Collection in Progress |
| External AD/DHCP Connectivity | Implemented — Documentation in Progress |
| Security Policy Validation | In Progress |
| Final Failover Testing | Planned |
| Monitoring and Hardening | Planned |
| Final Documentation and Demo | Planned |

## Documentation

Detailed architecture, configurations, addressing plans, verification results and troubleshooting records will be added as each project phase is validated.

## Security Notice

This project was created in an authorized virtual lab environment for learning, testing and professional demonstration. All addresses, configurations and credentials are lab-specific. No production credentials or confidential organizational information are included.



## Troubleshooting Case Studies

- [FortiGate Redundant Interface Active Member Causing Gateway Reachability Failure](troubleshooting/fortigate-redundant-interface-active-member-issue.md)
