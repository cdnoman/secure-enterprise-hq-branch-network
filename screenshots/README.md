# Screenshots and Evidence

This folder contains sanitized screenshots and visual evidence for the Secure Enterprise HQ–Branch Network Lab.

The purpose of this folder is to support the lab documentation with real verification evidence, including firewall status, VPN status, switching verification, routing output, DHCP relay testing, policy validation, and troubleshooting proof.

> Note: All screenshots must be sanitized before publishing. Do not upload screenshots containing passwords, secrets, public IP addresses, license information, serial numbers, or sensitive details.

---

## 1. Planned Screenshot Categories

| Category | Purpose | Status |
|---|---|---|
| fortigate-ha | HQ FortiGate HA health, sync and failover status | Planned |
| vlan-trunking | VLAN, trunk and gateway verification | Planned |
| stp | Rapid-PVST forwarding/blocking evidence | Planned |
| ipsec-vpn | HQ-to-Branch VPN tunnel status and traffic counters | Planned |
| dhcp-ad-dns | DHCP relay, AD/DNS and client testing evidence | Planned |
| routing | Route table and gateway verification | Planned |
| firewall-policies | Policy hit count, allowed/denied traffic evidence | Planned |
| nat | NAT behavior and translation verification | Planned |
| troubleshooting | Before/after screenshots for troubleshooting cases | Planned |

---

## 2. Recommended Folder Structure

```text
screenshots/
│
├── fortigate-ha/
│
├── vlan-trunking/
│
├── stp/
│
├── ipsec-vpn/
│
├── dhcp-ad-dns/
│
├── routing/
│
├── firewall-policies/
│
├── nat/
│
└── troubleshooting/
3. Screenshot Naming Standard
Use clear and consistent file names.
Recommended examples:
fortigate-ha-status-ok.png
fortigate-ha-config-in-sync.png
hq-vlan20-gateway-ping-success.png
hq-core-trunk-verification.png
rapid-pvst-blocking-port.png
ipsec-vpn-tunnel-up.png
branch-to-hq-ping-success.png
dhcp-relay-vlan20-success.png
firewall-policy-hit-count.png
nat-translation-working.png

4. Sanitization Rules
Before uploading screenshots, remove or hide:
- Passwords
- Pre-shared keys
- Public IP addresses
- Admin usernames if sensitive
- Serial numbers
- License details
- Organization-sensitive names
- Any confidential production information
Allowed content:
- Lab device names
- Lab VLAN IDs
- Lab private IP addresses
- Sanitized interface names
- Command output used for learning
- General status screens
- Testing evidence
5. Evidence Guidelines
A good screenshot should clearly show:
- What was being tested
- Which device or service was involved
- The result of the test
- Success/failure status
- Relevant command output or dashboard status
Avoid uploading screenshots that are:
- Too zoomed out
- Too blurry
- Full of unrelated information
- Containing sensitive details
- Not connected to any documentation
6. How Screenshots Should Be Used
Screenshots should be referenced inside relevant documentation files.
Example markdown:
![FortiGate HA Status](../screenshots/fortigate-ha/fortigate-ha-status-ok.png)

Example usage inside a troubleshooting case:
## Evidence

The following screenshot shows that the FortiGate HA cluster was synchronized during validation.

![FortiGate HA Status](../screenshots/fortigate-ha/fortigate-ha-status-ok.png)

7. Current Priority
The first screenshots to collect should be:
1. FortiGate HA status OK
2. FortiGate configuration in sync
3. HQ VLAN gateway ping success
4. HQ trunk verification
5. Rapid-PVST blocking/forwarding state
6. DHCP relay success for VLAN 20
7. IPsec VPN tunnel status
8. Branch-to-HQ ping test
9. Firewall policy hit count
10. NAT/route verification
