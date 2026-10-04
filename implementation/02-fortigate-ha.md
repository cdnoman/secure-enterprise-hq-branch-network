# FortiGate High Availability Implementation

## 1. Objective

The objective of this section is to document the FortiGate High Availability implementation used in the Secure Enterprise HQ–Branch Network Lab.

The HQ site uses two FortiGate firewalls in an Active-Passive HA cluster to provide firewall redundancy, configuration synchronization, and improved availability for internal VLAN gateway and security services.

---

## 2. Design Summary

The Headquarters firewall layer is built using two FortiGate VM firewalls.

One firewall operates as the active/primary unit, while the second firewall operates as the standby/secondary unit.

The HA cluster provides:

- Firewall redundancy
- Configuration synchronization
- Failover capability
- Consistent VLAN gateway availability
- Centralized security policy enforcement
- Reduced single point of failure at the firewall layer

---

## 3. HA Mode

The HQ FortiGate cluster uses:

```text
Mode: Active-Passive

In Active-Passive HA:
Primary FortiGate
        ↓
Handles production traffic

Secondary FortiGate
        ↓
Stays synchronized and ready for failover

If the primary firewall fails, the secondary firewall can take over the active role.
4. Devices Involved
Device	Role
HQ-FW-PRIMARY	Active FortiGate firewall
HQ-FW-SECONDARY	Standby FortiGate firewall
HQ-CORE-SW1	Core switch connected to firewall internal trunk
HQ-CORE-SW2	Secondary core switch / redundant switching path
HQ Access Switches	Endpoint access layer


5. Interface Allocation
Due to FortiGate VM interface limitations, the design uses controlled interface allocation.
Interface	Purpose
port1	WAN, management access and IPsec transport
port2	802.1Q trunk carrying HQ VLAN traffic
port3	Dedicated HA heartbeat interface
port4	Reserved for future use


6. HA Heartbeat Design
The HA heartbeat uses a dedicated interface.
HQ-FW-PRIMARY port3
        ↔
HQ-FW-SECONDARY port3

The heartbeat link is used for:
- HA status monitoring
- Cluster communication
- Configuration synchronization
- Failover detection
- Device health checking
A dedicated heartbeat interface improves HA stability and avoids mixing heartbeat traffic with production VLAN traffic.
7. Internal VLAN Trunk
The internal VLAN traffic is carried over port2.
FortiGate HA Cluster port2
        ↓
HQ Core Switching Layer
        ↓
HQ VLANs

VLAN interfaces are created on FortiGate and bound to the internal trunk interface.
Example VLAN gateway concept:
VLAN 20 Users
Gateway: 10.10.20.1
Interface: VLAN 20 on FortiGate trunk

This allows FortiGate to act as the gateway and policy enforcement point for HQ VLANs.
8. HA Verified Characteristics
The following HA characteristics were verified during lab implementation:
HA Check	Status
HA health status	OK
Configuration synchronization	In sync
HA heartbeat interface	Dedicated port3
Session pickup	Enabled
HA checksum status	Matching
Monitored WAN interface	port1
Active/standby role	Verified


9. Expected HA Behavior
Expected behavior during normal operation:
HQ-FW-PRIMARY
        ↓
Active firewall handling traffic

HQ-FW-SECONDARY
        ↓
Standby firewall synchronized with primary

Expected behavior during failover:
Primary firewall failure
        ↓
Secondary firewall detects failure
        ↓
Secondary firewall becomes active
        ↓
Traffic continues through new active firewall

10. Configuration Summary
The FortiGate HA configuration includes:
- HA mode selection
- Group name
- Group ID
- Device priority
- Heartbeat interface
- Session pickup
- Interface monitoring
- Configuration synchronization
Sanitized conceptual example:
config system ha
    set mode a-p
    set group-name <HA-GROUP-NAME>
    set group-id <GROUP-ID>
    set hbdev <HEARTBEAT-INTERFACE> <PRIORITY>
    set session-pickup enable
    set monitor <MONITORED-INTERFACE>
end

Note: This is a sanitized conceptual example. Actual secrets, real identifiers, and sensitive values are not published.

11. Verification Commands
Useful FortiGate HA verification commands:
get system ha status
diagnose sys ha status
get system performance status
get system interface
show system ha

Useful connectivity checks:
execute ping <gateway-ip>
execute ping <remote-host>
diagnose sniffer packet any "host <ip-address>" 4

12. Validation Tests
Test	Expected Result	Status
HA status check	Cluster should show healthy status	Verified
Configuration sync	Both firewalls should be in sync	Verified
Heartbeat check	Heartbeat should be active on port3	Verified
VLAN gateway reachability	VLAN gateways should remain reachable	Verified
Interface monitoring	Monitored interface should be tracked	Verified
Failover behavior	Secondary should become active if primary fails	Planned
Post-failover traffic validation	VLAN traffic should continue after failover	Planned


13. Issues Faced
During lab implementation, one important issue was discovered related to FortiGate interface behavior.
The Core switch was unable to reach the FortiGate VLAN gateway even though trunking and STP appeared correct.
The issue was later traced to the FortiGate redundant interface active member selection. The firewall was forwarding through an unexpected member path.
That troubleshooting case is documented separately:
troubleshooting/fortigate-redundant-interface-active-member-issue.md

14. Lessons Learned
- HA status alone is not enough; traffic flow must also be tested.
- Dedicated heartbeat interfaces improve HA clarity and stability.
- VLAN gateways should be tested from the switching layer after HA configuration.
- Interface monitoring is important for failover behavior.
- Configuration synchronization should be verified after every major change.
- Active/standby role should be checked before and after failover testing.
- HA troubleshooting should include firewall status, switch path, MAC learning, and VLAN gateway reachability.
- Redundant interface behavior should not be confused with LACP or EtherChannel.
15. Current Status
Component	Status
HQ FortiGate HA pair	Implemented
HA health status	Verified
Configuration synchronization	Verified
Dedicated heartbeat	Implemented
Session pickup	Enabled
WAN interface monitoring	Configured
VLAN gateway availability	Verified
Full failover test	Planned
Post-failover traffic evidence	Pending


16. One-Line Summary
The HQ FortiGate HA cluster provides firewall redundancy and synchronized security control for the enterprise lab, with VLAN gateways hosted on the firewall and heartbeat communication carried over a dedicated interface.




