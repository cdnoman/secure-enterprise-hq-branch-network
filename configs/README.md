# Configuration Files

This folder contains sanitized configuration snippets for the Secure Enterprise HQ–Branch Network Lab.

The purpose of this folder is to document important configuration examples used in the lab, including FortiGate firewall settings, Cisco switching configuration, VLAN trunking, routing, IPsec VPN, NAT, firewall policies, and related validation commands.

> Note: All configurations are sanitized before publishing. Passwords, secrets, real public IP addresses, private credentials, and sensitive values must not be uploaded.

---

## 1. Planned Configuration Files

| File / Folder | Purpose | Status |
|---|---|---|
| fortigate/hq-primary-firewall.md | HQ primary FortiGate configuration summary | Planned |
| fortigate/hq-secondary-firewall.md | HQ secondary FortiGate HA configuration summary | Planned |
| fortigate/branch-firewall.md | Branch FortiGate configuration summary | Planned |
| switching/hq-core-sw1.md | HQ Core Switch 1 configuration summary | Planned |
| switching/hq-core-sw2.md | HQ Core Switch 2 configuration summary | Planned |
| switching/hq-access-switches.md | HQ access switch VLAN/trunk configuration | Planned |
| switching/branch-switching.md | Branch core/distribution/access switching configuration | Planned |
| vpn/ipsec-hq-to-branch.md | HQ-to-Branch IPsec VPN configuration notes | Planned |
| firewall-policies/security-policies.md | Firewall security policy examples | Planned |
| nat/nat-configuration.md | NAT configuration and validation notes | Planned |
| routing/routing-summary.md | Static routes, gateway and route validation | Planned |

---

## 2. Recommended Folder Structure

```text
configs/
│
├── fortigate/
│   ├── hq-primary-firewall.md
│   ├── hq-secondary-firewall.md
│   └── branch-firewall.md
│
├── switching/
│   ├── hq-core-sw1.md
│   ├── hq-core-sw2.md
│   ├── hq-access-switches.md
│   └── branch-switching.md
│
├── vpn/
│   └── ipsec-hq-to-branch.md
│
├── firewall-policies/
│   └── security-policies.md
│
├── nat/
│   └── nat-configuration.md

3. Configuration Documentation Standard
Each configuration file should include:
- Configuration objective
- Devices involved
- Interfaces used
- VLANs or subnets involved
- Important commands or GUI settings
- Verification commands
- Testing result
- Notes and lessons learned
4. Sanitization Rules
Before uploading any configuration, remove or replace:
- Passwords
- Pre-shared keys
- Admin usernames
- Real public IP addresses
- Real organization names
- Real production hostnames
- API keys
- License details
- Serial numbers
- Any confidential information
Use placeholders instead:
<PUBLIC-IP>
<PRIVATE-SUBNET>
<REMOTE-PEER-IP>
<PRE-SHARED-KEY>
<ADMIN-USERNAME>
<INTERFACE-NAME>

5. Example Configuration Format
Use this format inside configuration files:
# Configuration Title

## 1. Objective

## 2. Devices Involved

## 3. Interfaces / VLANs

## 4. Configuration Summary

## 5. Important Commands or GUI Steps

## 6. Verification Commands

## 7. Testing Result

## 8. Notes

6. Example Verification Commands
Cisco switching:
show vlan brief
show interfaces trunk
show spanning-tree vlan <vlan-id>
show mac address-table
show ip interface brief
show ip route

FortiGate:
get system interface
get router info routing-table all
get system ha status
get vpn ipsec tunnel summary
diagnose vpn tunnel list
diagnose sniffer packet any "host <ip-address>" 4
diagnose debug flow

7. Current Priority
The first configuration files to document should be:
1. HQ FortiGate HA configuration summary
2. HQ VLAN trunk configuration
3. HQ Core Switch 1 configuration
4. HQ Core Switch 2 configuration
5. Branch switching configuration
6. HQ-to-Branch IPsec VPN configuration
7. Firewall policy and NAT configuration
│
└── routing/
    └── routing-summary.md
