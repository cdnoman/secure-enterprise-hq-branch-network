# Network Architecture Overview

> This document describes the current architecture of the Secure Enterprise HQ-Branch Network Lab, including FortiGate HA, VLAN segmentation, switching redundancy, external AD/DHCP integration, branch design, and site-to-site IPsec connectivity.

## 1. Project Scenario

This project represents a secure enterprise network consisting of a headquarters and a remote branch office.

The environment is designed to demonstrate enterprise switching, network segmentation, firewall high availability, secure site-to-site connectivity, external infrastructure-service integration, redundancy, testing and troubleshooting.

The complete environment is implemented in EVE-NG, while the Active Directory/DHCP server and Windows domain client run externally in VMware Workstation.

## 2. Design Constraint

The FortiGate VM image available in the lab provides only four physical interfaces. The architecture was therefore designed around IEEE 802.1Q VLAN trunks and controlled interface utilization.

### HQ FortiGate Interface Allocation

| Interface | Role |
|---|---|
| port1 | WAN, administrative access and IPsec transport |
| port2 | 802.1Q trunk carrying HQ VLANs |
| port3 | Dedicated HA heartbeat |
| port4 | Reserved for future use |

## 3. Headquarters Architecture

The headquarters contains:

- Two FortiGate firewalls in an Active-Passive HA cluster
- Two core switches
- Two access switches
- Redundant access-to-core uplinks
- Multiple security and operational VLANs
- An external VMware-based Active Directory/DHCP server
- An external Windows domain client
- An internal EVE-NG test endpoint

### FortiGate High Availability

The HQ firewalls operate as an Active-Passive cluster.

Verified HA characteristics:

- HA health status: OK
- Configuration status: In sync
- Dedicated heartbeat interface: port3
- Session pickup: Enabled
- HA configuration checksums: Matching
- Monitored WAN interface: port1

The internal port2 interface carries VLAN-tagged traffic between the FortiGate cluster and the HQ switching infrastructure.

## 4. HQ VLAN Architecture

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

The FortiGate provides the default gateways for the routed VLANs and controls communication between network segments.

## 5. HQ Switching and Redundancy

The two HQ access switches are dual-homed to both core switches.

Rapid-PVST is enabled to provide a loop-free Layer 2 topology while retaining redundant physical paths.

Verified behaviour:

- HQ Core 1 is the root bridge for operational VLANs.
- Each access switch uses its Core 1 uplink as the forwarding path.
- The Core 2 uplinks remain available as STP-controlled standby paths.
- A disabled redundant uplink on HQ Access Switch 2 was identified during verification and restored.
- Rapid-PVST successfully placed the appropriate redundant interfaces into blocking state.

This provides controlled Layer 2 redundancy without creating switching loops.

## 6. External AD, DHCP and Windows Client

The Active Directory, DNS and DHCP services run on an external VMware Windows Server.

### Server

| Property | Value |
|---|---|
| Hostname | Server1 |
| IP address | 192.168.75.160/24 |
| Role | Active Directory, DNS and DHCP |
| Domain | noman.com |

### Windows Client

| Property | Value |
|---|---|
| Hostname | PC2 |
| IP address | 10.10.20.53/24 |
| VLAN | VLAN 20 – Users |
| Default gateway | 10.10.20.1 |
| DHCP server | 192.168.75.160 |
| DNS server | 192.168.75.160 |
| Domain | noman.com |

The Windows client successfully received its VLAN 20 address through FortiGate DHCP relay.

Live DNS and domain-controller reachability testing identified a connectivity issue between VLAN 20 and the external server network. Firewall-policy and return-routing verification are currently in progress.

## 7. Branch Architecture

The branch contains:

- One FortiGate firewall
- One core switch
- Two distribution switches
- Two access switches
- Redundant distribution-to-access connections
- Branch user and service VLANs
- Test endpoints

The branch uses the `10.20.0.0/16` addressing summary. Routed `/30` transit networks are used between the Branch FortiGate, core and distribution layers.

## 8. Site-to-Site Connectivity

A route-based IPsec tunnel named `HQ-TO-BRANCH` connects the headquarters and branch environments.

The tunnel configuration exists on the firewalls. Final Phase 2 validation, protected-subnet testing and evidence collection remain in progress.

## 9. Current Project Status

| Area | Status |
|---|---|
| HQ physical and logical architecture | Implemented |
| HQ FortiGate HA | Verified |
| HQ VLAN gateways | Verified |
| HQ core and access trunking | Verified |
| HQ Rapid-PVST redundancy | Verified |
| Disabled redundant uplink | Resolved |
| External DHCP relay | Verified |
| Live AD/DNS connectivity | Troubleshooting in progress |
| Branch switching | Baseline collection pending |
| IPsec end-to-end validation | Verification pending |
| Security hardening | Planned |
| Failover testing | Planned |
| Monitoring integration | Planned |

## 10. Next Documentation Items

The following areas will be documented in separate files as the lab progresses:

- Detailed VLAN segmentation plan
- FortiGate HA configuration notes
- HQ and Branch IPsec VPN validation
- Firewall security policy design
- NAT and route verification
- Branch switching baseline
- Failover testing evidence
- Troubleshooting case studies
- Sanitized topology diagrams
