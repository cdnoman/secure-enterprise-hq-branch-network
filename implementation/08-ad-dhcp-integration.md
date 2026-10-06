# AD/DHCP Integration Implementation

## 1. Objective

The objective of this section is to document the Active Directory, DNS, and DHCP integration used in the Secure Enterprise HQ–Branch Network Lab.

The lab uses an external VMware-based Windows Server to provide enterprise infrastructure services such as Active Directory Domain Services, DNS, and DHCP.

The purpose of this integration is to simulate a realistic enterprise environment where client devices receive IP addresses dynamically, resolve internal domain names, and communicate with domain services.

---

## 2. Design Summary

The Active Directory, DNS, and DHCP server runs outside EVE-NG in VMware Workstation.

The EVE-NG lab network connects to this external VMware-based infrastructure service.

The HQ client in VLAN 20 receives IP addressing through DHCP relay configured toward the external DHCP server.

High-level service flow:

```text
HQ VLAN 20 Client
        ↓
FortiGate VLAN Gateway / DHCP Relay
        ↓
External VMware Network
        ↓
Windows Server 2022
        ↓
AD / DNS / DHCP Services

3. Devices Involved
Device	Platform	Role
Server1	Windows Server 2022	Active Directory, DNS and DHCP server
PC2	Windows Domain Client	Test client in HQ VLAN 20
HQ FortiGate HA Cluster	FortiGate VM	VLAN gateway and DHCP relay point
HQ Switching Layer	Cisco IOL	VLAN transport between client and firewall
VMware Workstation	External virtualization platform	Hosts Windows Server and client components


4. Server Details
Property	Value
Hostname	Server1
Platform	Windows Server 2022
IP Address	192.168.75.160/24
Role	Active Directory Domain Services, DNS and DHCP
Domain	noman.com


5. Client Details
Property	Value
Hostname	PC2
Platform	Windows Domain Client
VLAN	VLAN 20 – Users
IP Address	DHCP: 10.10.20.53/24
Default Gateway	10.10.20.1
DHCP Server	192.168.75.160
DNS Server	192.168.75.160
Domain	noman.com


6. DHCP Relay Design
Because the DHCP server is not located directly inside VLAN 20, DHCP relay is required.
The client broadcasts a DHCP request inside VLAN 20.
The FortiGate VLAN 20 interface forwards the DHCP request to the external DHCP server.
Expected DHCP relay flow:
PC2 in VLAN 20
        ↓
DHCP Discover Broadcast
        ↓
FortiGate VLAN 20 Gateway
        ↓
DHCP Relay / IP Helper
        ↓
Server1 DHCP Service
        ↓
DHCP Offer / Lease
        ↓
PC2 receives IP address

7. DHCP Scope Concept
The DHCP server provides IP addressing for VLAN 20 clients.
Example DHCP scope concept:
Scope Network: 10.10.20.0/24
Default Gateway: 10.10.20.1
DNS Server: 192.168.75.160
Domain Name: noman.com

The client successfully received an address from the DHCP scope.
Verified client result:
Client: PC2
Assigned IP: 10.10.20.53/24
Gateway: 10.10.20.1
DNS: 192.168.75.160

8. DNS Integration
DNS is provided by the Windows Server.
DNS is required for:
- Domain name resolution
- Active Directory functionality
- Domain controller discovery
- Client authentication
- Internal service lookup
Expected DNS flow:
Client
   ↓
DNS Query
   ↓
Windows DNS Server
   ↓
DNS Response

DNS testing should include:
nslookup noman.com
nslookup Server1
ping Server1
ping noman.com

9. Active Directory Integration
Active Directory is used to simulate enterprise domain services.
The Windows client is intended to communicate with the domain controller for:
- Domain authentication
- DNS-based domain discovery
- Group policy testing
- Domain join/testing
- Enterprise service validation
Important AD-related dependencies include:
- DNS reachability
- Domain controller reachability
- Time synchronization
- Required firewall policies
- Correct client DNS settings
- Routing and return path
10. Firewall Policy Requirements
For AD/DNS/DHCP integration, the firewall must allow required communication between VLAN 20 and the external server network.
Required traffic may include:
Service	Purpose
DHCP Relay	IP address assignment
DNS	Name resolution
ICMP	Basic reachability testing
LDAP/Kerberos	Active Directory authentication
SMB/RPC	Domain-related communication
NTP/Time	Time synchronization


Firewall policy validation should include:
- VLAN 20 to Server1 traffic
- Server1 to VLAN 20 return traffic
- DNS traffic
- DHCP relay behavior
- Domain-related traffic
- Logs and policy hit count
11. Routing Requirements
Routing must work in both directions.
Required path:
VLAN 20 Client
        ↓
FortiGate VLAN Gateway
        ↓
External VMware / Server Network
        ↓
Server1

Return path:
Server1
        ↓
External VMware / Server Network
        ↓
FortiGate / Lab Network
        ↓
VLAN 20 Client

A missing return route can cause the client to send traffic successfully while replies fail to return.
12. Verification Commands
Useful Windows client checks:
ipconfig /all
ipconfig /release
ipconfig /renew
nslookup noman.com
nslookup Server1
ping 10.10.20.1
ping 192.168.75.160

Useful Windows Server checks:
Check DHCP service status
Check DHCP scope leases
Check DNS service status
Check DNS records
Check domain controller role
Check Windows Firewall rules

Useful FortiGate checks:
get system interface
get router info routing-table all
show firewall policy
diagnose debug flow
diagnose sniffer packet any "host 192.168.75.160" 4
execute ping 192.168.75.160

Useful Cisco switching checks:
show vlan brief
show interfaces trunk
show mac address-table vlan 20
show spanning-tree vlan 20

13. Validation Tests
Test	Expected Result	Status
VLAN 20 client receives DHCP IP	Client should receive 10.10.20.x address	Verified
Client gets correct gateway	Gateway should be 10.10.20.1	Verified
Client gets correct DNS server	DNS should be 192.168.75.160	Verified
Client reaches VLAN gateway	Ping to 10.10.20.1 should work	Verified
Client reaches DHCP server	Ping to 192.168.75.160 should work	In Progress
DNS lookup	Client should resolve domain/server names	In Progress
Domain connectivity	Client should reach domain services	In Progress
Firewall policy hit count	Policy should match AD/DNS/DHCP traffic	In Progress
Return routing	Server replies should return to VLAN 20	In Progress


14. Issues Faced
The Windows client successfully received its VLAN 20 address through DHCP relay.
However, live AD/DNS connectivity testing identified that additional verification was required between VLAN 20 and the external server network.
Possible areas under investigation include:
- Firewall policy between VLAN 20 and external server network
- Return routing from external VMware network to VLAN 20
- DNS reachability
- Windows Firewall behavior
- AD service reachability
- FortiGate route and policy behavior
- VMware network path
15. Troubleshooting Notes
Important troubleshooting points:
- DHCP success does not always mean full AD/DNS reachability
- DHCP relay may work while DNS or domain traffic still fails
- Client DNS must point to the AD/DNS server
- Return routing from external server network must be verified
- Windows Firewall can affect testing
- Firewall policy must allow required AD/DNS traffic
- DNS resolution is critical for Active Directory
- ICMP success is useful but does not prove all AD services are working
16. Security Considerations
AD/DNS/DHCP services are sensitive infrastructure services.
Recommended controls:
- Allow only required VLANs to reach infrastructure services
- Restrict unnecessary access to the domain controller
- Log important infrastructure traffic
- Avoid broad Any-to-Any rules
- Use clear firewall policy names
- Separate management access from user access
- Document required ports and services
- Monitor authentication and DNS-related traffic
17. Current Status
Component	Status
External Windows Server	Operational
AD DS role	Installed / Available
DNS service	Available
DHCP service	Available
DHCP relay for VLAN 20	Verified
VLAN 20 client IP assignment	Verified
DNS reachability	In Progress
AD/domain connectivity	In Progress
Firewall policy validation	In Progress
Return routing verification	In Progress
Final screenshots	Pending


18. One-Line Summary
The lab successfully integrates an external VMware-based AD/DNS/DHCP server with HQ VLAN 20 through DHCP relay, while DNS, domain reachability, firewall policy, and return-routing validation remain in progress.
