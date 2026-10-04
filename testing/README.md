# Testing and Validation

This folder documents the testing and validation process for the Secure Enterprise HQ–Branch Network Lab.

The purpose of this section is to prove that the lab is not only configured, but also tested through verification commands, connectivity checks, failover validation, firewall policy checks, VPN testing, and troubleshooting evidence.

---

## 1. Testing Areas

| Testing Area | Purpose | Status |
|---|---|---|
| HQ VLAN Gateway Testing | Verify that HQ VLAN gateways are reachable | Verified |
| HQ Trunk Testing | Verify VLAN trunking between FortiGate, core and access switches | Verified |
| HQ Rapid-PVST Testing | Verify loop-free redundant switching paths | Verified |
| HQ FortiGate HA Testing | Verify HA health, sync and failover readiness | Verified |
| External DHCP Relay Testing | Verify DHCP relay from VLAN 20 to external DHCP server | Verified |
| AD/DNS Connectivity Testing | Verify domain and DNS reachability from VLAN users | In Progress |
| Branch Switching Testing | Verify Branch core, distribution and access switching | In Progress |
| HSRP Testing | Verify Branch gateway redundancy | In Progress |
| IPsec VPN Testing | Verify HQ-to-Branch encrypted connectivity | In Progress |
| Firewall Policy Testing | Verify allowed and denied traffic flows | In Progress |
| NAT Testing | Verify correct translation behavior | In Progress |
| Failover Testing | Verify redundancy during link/device failure | Planned |

---

## 2. Planned Testing Documents

The following testing documents will be added as the lab progresses:

```text
01-hq-vlan-gateway-testing.md
02-hq-trunk-and-stp-testing.md
03-fortigate-ha-testing.md
04-dhcp-relay-testing.md
05-ad-dns-connectivity-testing.md
06-branch-switching-testing.md
07-hsrp-testing.md
08-ipsec-vpn-testing.md
09-firewall-policy-testing.md
10-nat-testing.md
11-failover-testing.md

3. Testing Methodology
Each testing document should include:
- Test objective
- Devices involved
- Source and destination
- Commands used
- Expected result
- Actual result
- Screenshots or output evidence
- Issue found, if any
- Final status
4. Testing Template
Each testing file should follow this structure:
# Test Title

## 1. Objective

## 2. Devices Involved

## 3. Test Scenario

## 4. Expected Result

## 5. Commands / Checks Used

## 6. Actual Result

## 7. Evidence

## 8. Issue Found

## 9. Fix Applied

## 10. Final Status

5. Example Validation Commands
Common switching checks:
show interfaces status
show interfaces trunk
show vlan brief
show spanning-tree vlan <vlan-id>
show mac address-table
show ip interface brief
ping <gateway-ip>

Common FortiGate checks:
get system ha status
get system interface
get router info routing-table all
diagnose sniffer packet any "host <ip-address>" 4
diagnose debug flow
execute ping <destination-ip>

Common VPN checks:
get vpn ipsec tunnel summary
diagnose vpn tunnel list
diagnose vpn ike gateway list
get router info routing-table all
diagnose sniffer packet any "host <source-or-destination-ip>" 4

6. Evidence Guidelines
Testing evidence can include:
- Sanitized screenshots
- CLI command output
- Ping results
- Traceroute results
- Firewall logs
- VPN tunnel status
- HA status
- STP state
- Routing table output
Do not include:
- Passwords
- Real public IP addresses
- Confidential organization data
- Unnecessary sensitive topology details
7. Current Priority
The current testing priority is:
1. Complete AD/DNS connectivity troubleshooting
2. Validate HQ-to-Branch IPsec traffic
3. Capture VPN tunnel evidence
4. Verify Branch HSRP behavior
5. Document firewall policy and NAT testing
6. Capture final screenshots for portfolio use
