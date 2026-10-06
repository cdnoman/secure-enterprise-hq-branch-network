# NAT Configuration Implementation

## 1. Objective

The objective of this section is to document the Network Address Translation design used in the Secure Enterprise HQ–Branch Network Lab.

NAT is used to translate internal private IP addresses when traffic goes toward external networks such as the internet.

In this lab, NAT is also reviewed carefully to make sure that internal traffic, VPN traffic, and infrastructure service traffic are not translated incorrectly.

---

## 2. Design Summary

The FortiGate firewall is responsible for NAT control in the lab.

NAT is mainly required for:

- User VLAN internet access
- Guest VLAN internet access
- Selected infrastructure internet access
- Controlled outbound access from internal networks

NAT is usually not required for:

- Internal VLAN-to-VLAN traffic
- HQ-to-Branch VPN traffic
- AD/DNS/DHCP internal communication
- Management traffic between internal networks

The main goal is to apply NAT only where required.

---

## 3. NAT Design Principle

The NAT design follows this principle:

```text
Internet-bound private traffic = NAT required
Internal private-to-private traffic = NAT usually not required
VPN site-to-site traffic = NAT usually not required

This helps avoid common issues where traffic is allowed by firewall policy but still fails because NAT is missing, incorrect, or applied where it should not be.
4. NAT Traffic Categories
Traffic Type	NAT Requirement	Notes
User VLAN to Internet	Required	Source NAT to WAN interface or public IP
Guest VLAN to Internet	Required	Restricted internet-only access
Server VLAN to Internet	Depends on design	Usually controlled and limited
Management VLAN to Internet	Depends on design	Should be restricted
HQ VLAN to Branch VLAN over VPN	Not normally required	NAT should usually be disabled
Branch VLAN to HQ VLAN over VPN	Not normally required	NAT should usually be disabled
User VLAN to AD/DNS/DHCP	Not required	Internal/private communication
Inter-VLAN traffic	Not normally required	Controlled by firewall policies


5. Internet NAT Flow
Expected user internet flow:
User VLAN
   ↓
FortiGate Firewall Policy
   ↓
Source NAT Applied
   ↓
WAN Interface
   ↓
Internet

Example NAT behavior:
Internal user IP: 10.10.20.53
Translated IP:   WAN interface IP / assigned public IP
Destination:     Internet

This allows private internal addresses to communicate with external networks.
6. VPN NAT Consideration
For site-to-site VPN, NAT must be reviewed carefully.
Expected VPN traffic flow:
HQ VLAN
   ↓
FortiGate Policy
   ↓
IPsec VPN Tunnel
   ↓
Branch VLAN

Important concept:
HQ-to-Branch VPN traffic should usually keep its original private source IP.

If VPN traffic is accidentally NATed, the remote side may see an unexpected source IP. This can cause routing, policy, or return-path issues.
7. Example Internet NAT Policy
Conceptual policy:
Source Zone: USERS
Destination Zone: WAN
Source Address: User VLAN subnet
Destination Address: Internet / Any
Service: Required web services
Action: Allow
NAT: Enabled
Translation: Outgoing interface IP
Logging: Enabled

Purpose:
- Allow user internet access
- Translate private source IPs
- Log traffic for troubleshooting
- Keep internet access controlled
8. Example Guest NAT Policy
Conceptual guest internet policy:
Source Zone: GUEST
Destination Zone: WAN
Source Address: Guest VLAN subnet
Destination Address: Internet / Any
Service: Web / DNS / Required Services
Action: Allow
NAT: Enabled
Translation: Outgoing interface IP
Logging: Enabled

Guest access should be restricted from internal networks.
Recommended behavior:
Guest VLAN → Internet: Allowed with NAT
Guest VLAN → Internal VLANs: Denied

9. Example VPN No-NAT Policy
Conceptual VPN policy:
Source Zone: HQ_INTERNAL
Destination Zone: VPN
Source Address: HQ subnet
Destination Address: Branch subnet
Service: Required services
Action: Allow
NAT: Disabled
Logging: Enabled

Return direction:
Source Zone: VPN
Destination Zone: HQ_INTERNAL
Source Address: Branch subnet
Destination Address: HQ subnet
Service: Required services
Action: Allow
NAT: Disabled
Logging: Enabled

This preserves the real private IP addresses across the VPN tunnel.
10. NAT and Firewall Policy Relationship
Firewall policy and NAT are related, but they are not the same.
Firewall policy = decides whether traffic is allowed or denied
NAT = decides whether source/destination IP translation happens

A policy can allow traffic, but traffic may still fail if NAT is missing or wrong.
Example:
Policy: Allow
Route: Present
NAT: Missing
Result: Internet access may fail

Another example:
Policy: Allow
VPN Route: Present
NAT: Accidentally enabled
Result: VPN traffic may fail

11. Common NAT Issues
Common NAT-related issues include:
- NAT missing for internet-bound traffic
- NAT enabled for VPN traffic
- Wrong source zone in NAT rule
- Wrong source subnet/address object
- Wrong outgoing interface
- NAT rule order issue
- Overlapping NAT rule matching first
- Wrong translated IP
- Return traffic not matching expected session
- Traffic allowed by policy but not translated
12. Verification Commands and Checks
Useful FortiGate checks:
show firewall policy
show firewall central-snat-map
diagnose sys session list
diagnose firewall iprope lookup
diagnose debug flow
diagnose sniffer packet any "host <ip-address>" 4
get router info routing-table all

Useful traffic checks:
ping 8.8.8.8
traceroute 8.8.8.8
nslookup google.com
curl ifconfig.me
Test from user VLAN
Test from guest VLAN
Test from server VLAN
Test across VPN tunnel

GUI areas to check:
Policy & Objects > Firewall Policy
Policy & Objects > NAT / Central SNAT
Network > Interfaces
Network > Static Routes
Log & Report > Forward Traffic
VPN > IPsec Tunnels

13. Validation Tests
Test	Expected Result	Status
User VLAN internet access	Traffic should be allowed and NATed	In Progress
Guest VLAN internet access	Traffic should be allowed and NATed	Planned
Server VLAN internet access	Should follow controlled policy	Planned
HQ-to-Branch VPN traffic	Should pass without unwanted NAT	Verification Pending
Branch-to-HQ VPN traffic	Should pass without unwanted NAT	Verification Pending
NAT session verification	Sessions should show expected translation	In Progress
Policy hit count	Correct policy should match traffic	In Progress
Deny/drop log check	Logs should help identify failures	In Progress


14. Troubleshooting Methodology
When NAT-related traffic fails, follow this path:
Source endpoint
   ↓
Gateway
   ↓
Firewall policy
   ↓
NAT rule
   ↓
Route lookup
   ↓
Destination
   ↓
Return path

Important questions:
Is the traffic matching the correct firewall policy?
Is NAT enabled where it is required?
Is NAT disabled where it should not be used?
Is the correct source zone selected?
Is the correct source subnet selected?
Is the correct outgoing interface selected?
Is another NAT rule matching before the expected rule?
Is the return traffic coming back?
Are session logs showing translation?

15. NAT Best Practices
Recommended best practices:
- Document which VLANs require internet NAT
- Keep VPN traffic exempt from unnecessary NAT
- Use clear NAT policy names
- Avoid broad NAT rules without purpose
- Match NAT rules to source zones and subnets
- Review NAT order after every change
- Validate NAT with session/log checks
- Test from each VLAN separately
- Compare working and non-working sources
- Document every special NAT exception
16. Relation to Troubleshooting Case Studies
NAT-related mistakes are common in real-world troubleshooting.
A related production-style case study is:
Firewall Policy Allowed but Internet Still Not Working Due to NAT Issue

The main lesson from that case is:
A firewall policy hit only proves that policy matched. It does not prove NAT is correct.

17. Current Status
Component	Status
Basic internet NAT concept	Documented
User VLAN NAT	In Progress
Guest VLAN NAT	Planned
VPN no-NAT validation	Verification Pending
NAT rule order review	In Progress
Session/log verification	In Progress
Screenshot evidence	Pending
Final NAT validation	Pending


18. One-Line Summary
NAT in this lab is used for internet-bound private traffic, while internal and VPN traffic must be carefully protected from unnecessary translation to avoid routing, policy, and return-path issues.
