# Firewall Policy Implementation

## 1. Objective

The objective of this section is to document the firewall policy design used in the Secure Enterprise HQ–Branch Network Lab.

Firewall policies are used to control traffic between VLANs, security zones, WAN interfaces, VPN tunnels, and infrastructure services.

The goal is to ensure that traffic is allowed only where required and denied where it is not needed.

---

## 2. Design Summary

The FortiGate firewalls act as the main security control points in the lab.

They are responsible for:

- Inter-VLAN traffic control
- Internet access control
- VPN traffic control
- Branch-to-HQ traffic filtering
- NAT policy enforcement
- Security zone separation
- Logging and policy verification

The HQ FortiGate HA cluster controls traffic between HQ VLANs and external networks.

The Branch FortiGate controls Branch traffic and VPN connectivity toward HQ.

---

## 3. Security Zone Concept

Traffic is controlled using source and destination zones/interfaces.

Example logical zones include:

| Zone | Purpose |
|---|---|
| WAN | Internet / transport network |
| USERS | User endpoint VLANs |
| SERVERS | Internal server VLANs |
| MANAGEMENT | Network management VLAN |
| DMZ | Public-facing or semi-trusted services |
| GUEST | Guest network |
| IOT | IoT devices |
| SECURITY | Security tools and monitoring |
| VPN | Site-to-site VPN tunnel traffic |
| INFRA | Infrastructure services such as AD/DNS/DHCP |

---

## 4. Policy Design Principle

The firewall policy design follows a least-privilege approach.

This means:

```text
Allow only required traffic.
Deny everything else by default.

Each policy should clearly define:
- Source zone/interface
- Destination zone/interface
- Source address
- Destination address
- Service/application
- Schedule
- NAT requirement
- Logging
- Security profile if required
5. Example Policy Flow
Example user internet access flow:
Users VLAN
   ↓
FortiGate Firewall Policy
   ↓
SNAT / NAT
   ↓
WAN
   ↓
Internet

Example server access flow:
Users VLAN
   ↓
FortiGate Firewall Policy
   ↓
Servers VLAN
   ↓
Internal Server

Example VPN flow:
Branch Users
   ↓
Branch Firewall Policy
   ↓
IPsec VPN Tunnel
   ↓
HQ Firewall Policy
   ↓
HQ Internal Services

6. Policy Categories
The lab firewall policies can be grouped into the following categories:
Policy Category	Purpose
User-to-Internet	Allows selected user VLANs to access internet
User-to-Server	Allows controlled access from users to internal servers
Management Access	Allows admin/management access to network devices
DHCP/DNS/AD Services	Allows clients to reach infrastructure services
Guest Internet	Allows guest users to access internet only
IoT Restricted Access	Limits IoT devices to required services
DMZ Access	Controls access to/from DMZ services
VPN Traffic	Allows HQ and Branch networks to communicate
Security/Monitoring	Allows monitoring and security tools to reach required devices
Deny/Block Rules	Blocks unauthorized or unnecessary traffic


7. Example Internet Access Policy
Conceptual policy:
Source Zone: USERS
Destination Zone: WAN
Source Address: User VLAN subnet
Destination Address: Internet / Any
Service: Required web services
Action: Allow
NAT: Enabled
Logging: Enabled

Purpose:
- Allow user internet access
- Apply NAT
- Log traffic for visibility
- Keep policy scope controlled
8. Example Internal Access Policy
Conceptual policy:
Source Zone: USERS
Destination Zone: SERVERS
Source Address: User VLAN subnet
Destination Address: Required server subnet/object
Service: Required application ports
Action: Allow
NAT: Disabled
Logging: Enabled

Purpose:
- Allow users to access approved internal servers
- Avoid unnecessary broad access
- Keep internal traffic visible through logs
9. Example VPN Policy
Conceptual HQ-side VPN policy:
Source Zone: VPN
Destination Zone: HQ_INTERNAL
Source Address: Branch subnet
Destination Address: HQ subnet/service
Service: Required services
Action: Allow
NAT: Disabled
Logging: Enabled

Conceptual Branch-side VPN policy:
Source Zone: BRANCH_INTERNAL
Destination Zone: VPN
Source Address: Branch subnet
Destination Address: HQ subnet/service
Service: Required services
Action: Allow
NAT: Disabled
Logging: Enabled

Important note:
VPN traffic usually should not be source NATed unless the design specifically requires it.

10. AD/DNS/DHCP Policy Considerations
Infrastructure services such as AD, DNS, and DHCP require careful firewall policy planning.
Required services may include:
Service	Purpose
DNS	Name resolution
DHCP Relay	IP address assignment through relay
LDAP/Kerberos	Domain authentication
SMB	File/domain-related services
NTP	Time synchronization
ICMP	Testing and reachability validation


In this lab, the external VMware server provides AD/DNS/DHCP services for testing.
The firewall policy must allow the required communication between VLAN clients and the infrastructure server.
11. NAT Consideration
NAT is not required for every policy.
General NAT logic:
User VLAN → Internet = NAT usually required
Internal VLAN → Internal VLAN = NAT usually not required
HQ VLAN → Branch VLAN over VPN = NAT usually not required
Guest VLAN → Internet = NAT usually required
Monitoring/Infrastructure → Internet = NAT depends on design

Incorrect NAT behavior can cause traffic failure even when firewall policy is allowing traffic.
12. Logging and Visibility
Logging should be enabled for important policies.
Logging helps verify:
- Which policy matched the traffic
- Whether traffic was allowed or denied
- Source and destination details
- Service/port used
- NAT behavior
- Troubleshooting evidence
For troubleshooting, policy hit count and traffic logs are important.
13. Verification Commands and Checks
Useful FortiGate checks:
show firewall policy
diagnose firewall iprope lookup
diagnose sys session list
diagnose debug flow
diagnose sniffer packet any "host <ip-address>" 4
get router info routing-table all

Useful GUI checks:
Policy & Objects > Firewall Policy
Log & Report > Forward Traffic
Network > Interfaces
Network > Static Routes
VPN > IPsec Tunnels

Useful traffic tests:
ping <destination-ip>
traceroute <destination-ip>
nslookup <domain-name>
Test application access
Check policy hit count
Check allow/deny logs

14. Validation Tests
Test	Expected Result	Status
User VLAN to gateway	Gateway should be reachable	Verified
User VLAN to internet	Should work when policy and NAT are correct	In Progress
User VLAN to AD/DNS/DHCP	Required services should be reachable	In Progress
Guest VLAN to internal VLANs	Should be restricted	Planned
IoT VLAN to internal services	Should be restricted/controlled	Planned
HQ to Branch VPN traffic	Should match VPN policies	Verification Pending
Branch to HQ VPN traffic	Should match VPN policies	Verification Pending
Policy hit count	Should increase during testing	In Progress
Deny logs	Should help identify blocked traffic	In Progress


15. Common Issues
Common firewall policy issues include:
- Wrong source zone
- Wrong destination zone
- Wrong source address object
- Wrong destination address object
- Missing service/port
- NAT enabled when it should be disabled
- NAT disabled when it should be enabled
- Policy order issue
- Route missing
- Return route missing
- Traffic matching a deny policy
- Security profile blocking traffic
16. Troubleshooting Notes
When traffic fails, do not check only the firewall policy.
Follow the full packet path:
Source endpoint
   ↓
Default gateway
   ↓
Firewall policy
   ↓
NAT decision
   ↓
Routing decision
   ↓
Destination
   ↓
Return path

Important questions:
Is the source zone correct?
Is the destination zone correct?
Is the route present?
Is the policy matching?
Is NAT correct?
Is return traffic coming back?
Is another policy matching before the expected policy?
Are logs showing allow or deny?

17. Security Best Practices
Recommended best practices:
- Use clear policy names
- Avoid overly broad Any-to-Any rules
- Enable logging on important policies
- Keep deny rules visible where useful
- Use address objects and service objects
- Place specific rules above general rules
- Review NAT and policy together
- Document why each policy exists
- Remove unused policies
- Test traffic after every policy change
18. Current Status
Component	Status
Basic firewall policy framework	Implemented
Internet access policies	In Progress
AD/DNS/DHCP access policies	In Progress
VPN policies	Verification Pending
NAT review	In Progress
Logging validation	In Progress
Deny/drop troubleshooting	In Progress
Final screenshots	Pending


19. One-Line Summary
Firewall policies in this lab control traffic between VLANs, WAN, VPN, and infrastructure services, while NAT, routing, and return-path validation are required to confirm that allowed traffic actually works.
