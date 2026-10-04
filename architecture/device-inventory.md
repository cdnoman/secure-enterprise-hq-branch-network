# Device Inventory

> This document identifies the devices used in the Secure Enterprise HQ–Branch Network Lab and explains their role in the overall architecture.

The lab includes a headquarters site, a branch site, and external VMware-based infrastructure services used to simulate a real enterprise environment.

---

## 1. Headquarters Devices

| Device Name | Platform | Role | Status |
|---|---|---|---|
| HQ-FW-PRIMARY | FortiGate-VM64-KVM | Active firewall in the HQ Active-Passive HA cluster | Operational |
| HQ-FW-SECONDARY | FortiGate-VM64-KVM | Standby firewall providing HQ firewall redundancy | Operational |
| HQ-CORE-SW1 | Cisco IOL Switch | Primary HQ core switch and Rapid-PVST root for operational VLANs | Operational |
| HQ-CORE-SW2 | Cisco IOL Switch | Secondary HQ core switch providing redundant switching paths | Operational |
| HQ-ACCESS-SW1 | Cisco IOL Layer 2 Switch | Connects HQ user endpoints and external VMware client network | Operational |
| HQ-ACCESS-SW2 | Cisco IOL Layer 2 Switch | Provides additional HQ access-layer connectivity and redundant uplinks | Operational |
| HQ-TEST-PC1 | VPCS | Internal endpoint used for VLAN and connectivity testing | Operational |

---

## 2. Branch Devices

| Device Name | Platform | Role | Status |
|---|---|---|---|
| BR-FW1 | FortiGate-VM64-KVM | Branch firewall and site-to-site IPsec VPN peer | Implemented |
| BR-CORE-SW1 | Cisco IOL Layer 3 Switch | Routes traffic between Branch FortiGate and distribution layer | Implemented |
| BR-DIST-SW1 | Cisco IOL Layer 3 Switch | Branch distribution switch and HSRP gateway participant | Implemented |
| BR-DIST-SW2 | Cisco IOL Layer 3 Switch | Redundant branch distribution switch and HSRP gateway participant | Implemented |
| BR-ACCESS-SW1 | Cisco IOL Layer 2 Switch | Connects Branch user and service endpoints | Implemented |
| BR-ACCESS-SW2 | Cisco IOL Layer 2 Switch | Provides redundant Branch access-layer connectivity | Implemented |
| BR-TEST-PC1 | VPCS | Branch endpoint used for VLAN, routing and VPN testing | Testing Pending |

---

## 3. External VMware Devices

| Device Name | Platform | IP Address | Role | Status |
|---|---|---|---|---|
| Server1 | Windows Server 2022 | 192.168.75.160/24 | Active Directory Domain Services, DNS and DHCP | Operational |
| PC2 | Windows Domain Client | DHCP: 10.10.20.53/24 | HQ VLAN 20 domain client used for DHCP, DNS and authentication testing | Operational |

---

## 4. HQ FortiGate HA Interface Allocation

The available FortiGate VM image provides a limited number of physical interfaces, so the HQ design uses VLAN trunking to carry multiple internal networks over a single interface.

| Interface | Role | Notes |
|---|---|---|
| port1 | WAN, management access and IPsec transport | Used for external connectivity and VPN transport |
| port2 | 802.1Q trunk carrying HQ VLANs | Carries internal HQ VLAN-tagged traffic |
| port3 | Dedicated HA heartbeat | Used for FortiGate HA synchronization |
| port4 | Reserved and currently unused | Reserved for future expansion/testing |

---

## 5. Branch FortiGate Interface Allocation

| Interface | Role | Notes |
|---|---|---|
| port1 | WAN, management access and IPsec transport | Used for external connectivity and VPN transport |
| port2 | Connection to BR-CORE-SW1 | Internal branch connectivity |
| port3 | Reserved and currently unused | Reserved for future expansion/testing |
| port4 | Reserved and currently unused | Reserved for future expansion/testing |

---

## 6. Device Status Notes

- The HQ FortiGate cluster is operational and synchronized.
- HQ firewall HA heartbeat uses a dedicated interface.
- HQ internal VLANs are carried over a trunk interface due to FortiGate VM interface limitations.
- Both HQ access switches have redundant uplinks controlled by Rapid-PVST.
- Branch distribution switches participate in HSRP for gateway redundancy.
- The external Windows client successfully receives its VLAN 20 address through DHCP relay.
- Live AD/DNS connectivity testing is currently under investigation.
- Final Branch routing, IPsec and failover evidence is being collected.

---

## 7. Documentation Notes

This inventory will be updated as the lab progresses.

Planned additions include:

- Final branch IP addressing table
- VPN peer and tunnel documentation
- Firewall policy mapping
- NAT policy mapping
- Interface-to-device connection matrix
- Testing and validation screenshots
- Final topology diagrams
