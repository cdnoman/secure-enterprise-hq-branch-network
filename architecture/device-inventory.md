# Device Inventory

This inventory identifies the devices used in the Secure Enterprise HQ–Branch Network lab and explains their role in the architecture.

## Headquarters Devices

| Device Name | Platform | Role |
|---|---|---|
| HQ-FW-PRIMARY | FortiGate-VM64-KVM | Active firewall in the HQ Active-Passive HA cluster |
| HQ-FW-SECONDARY | FortiGate-VM64-KVM | Standby firewall providing HQ firewall redundancy |
| HQ-CORE-SW1 | Cisco IOL Switch | Primary HQ core switch and Rapid-PVST root for operational VLANs |
| HQ-CORE-SW2 | Cisco IOL Switch | Secondary HQ core switch providing redundant switching paths |
| HQ-ACCESS-SW1 | Cisco IOL Layer 2 Switch | Connects HQ user endpoints and the external VMware client network |
| HQ-ACCESS-SW2 | Cisco IOL Layer 2 Switch | Provides additional HQ access-layer connectivity and redundant uplinks |
| HQ-TEST-PC1 | VPCS | Internal endpoint used for VLAN and connectivity testing |

## Branch Devices

| Device Name | Platform | Role |
|---|---|---|
| BR-FW1 | FortiGate-VM64-KVM | Branch firewall and site-to-site IPsec VPN peer |
| BR-CORE-SW1 | Cisco IOL Layer 3 Switch | Routes traffic between the Branch FortiGate and distribution layer |
| BR-DIST-SW1 | Cisco IOL Layer 3 Switch | Branch distribution switch and HSRP gateway participant |
| BR-DIST-SW2 | Cisco IOL Layer 3 Switch | Redundant branch distribution switch and HSRP gateway participant |
| BR-ACCESS-SW1 | Cisco IOL Layer 2 Switch | Connects Branch user and service endpoints |
| BR-ACCESS-SW2 | Cisco IOL Layer 2 Switch | Provides redundant Branch access-layer connectivity |
| BR-TEST-PC1 | VPCS | Branch endpoint used for VLAN, routing and VPN testing |

## External VMware Devices

| Device Name | Platform | IP Address | Role |
|---|---|---|---|
| Server1 | Windows Server 2022 | 192.168.75.160/24 | Active Directory Domain Services, DNS and DHCP |
| PC2 | Windows Domain Client | DHCP: 10.10.20.53/24 | HQ VLAN 20 domain client used for DHCP, DNS and authentication testing |

## Firewall Interface Allocation

### HQ FortiGate HA Cluster

| Interface | Role |
|---|---|
| port1 | WAN, management access and IPsec transport |
| port2 | 802.1Q trunk carrying HQ VLANs |
| port3 | Dedicated HA heartbeat |
| port4 | Reserved and currently unused |

### Branch FortiGate

| Interface | Role |
|---|---|
| port1 | WAN, management access and IPsec transport |
| port2 | Connection to BR-CORE-SW1 |
| port3 | Reserved and currently unused |
| port4 | Reserved and currently unused |

## Device Status Notes

- The HQ FortiGate cluster is operational and synchronized.
- Both HQ access switches have active redundant uplinks controlled by Rapid-PVST.
- Branch distribution switches participate in HSRP.
- The external Windows client successfully receives its VLAN 20 address through DHCP relay.
- Live AD/DNS connectivity is currently under investigation.
- Final Branch routing, IPsec and failover evidence is being collected.
