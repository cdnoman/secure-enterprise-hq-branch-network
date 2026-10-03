# FortiGate Lab Case Study: Redundant Interface Active Member Causing Gateway Reachability Failure

> Note: This case study is based on an EVE-NG enterprise lab environment. IP addresses, interface names, and topology details are used for learning and portfolio documentation. This is a lab troubleshooting case, not a production incident.

## 1. Overview

This case study documents a FortiGate redundant interface troubleshooting issue in an enterprise HQ lab.

The Core switch was unable to ping the VLAN gateway hosted on the FortiGate firewall, even though the trunk configuration, VLAN configuration, and STP behavior appeared correct.

After investigation, the issue was traced to the FortiGate redundant interface member selection. The redundant interface was active on a member port connected toward a path that was not yet ready, while the expected active path toward Core1 was not being used.

The fix was to adjust the redundant interface member order so the correct FortiGate interface became the active member.

## 2. Lab Environment

The lab environment included:

- EVE-NG enterprise HQ topology
- FortiGate firewall
- Cisco Core switch
- VLAN-based internal network
- FortiGate redundant interface
- Trunk link between Core switch and FortiGate
- Multiple VLAN gateways on FortiGate
- Layer 2 switching and STP
- Inter-VLAN gateway testing

## 3. Expected Design

The expected traffic path was:

```text
Core Switch
   ↓
Trunk Link
   ↓
FortiGate Redundant Interface
   ↓
VLAN Gateway on FortiGate

The Core switch should be able to reach the FortiGate VLAN gateway through the active redundant interface member.
Expected behavior:
Core switch sends ARP/ping toward VLAN gateway
        ↓
FortiGate receives traffic on active redundant member
        ↓
FortiGate responds
        ↓
Core switch learns FortiGate MAC address
        ↓
Gateway ping succeeds

4. Problem Observed
Core1 was unable to ping the VLAN gateway on FortiGate.
Observed symptoms included:
- Core switch interface was up
- Trunk configuration appeared correct
- VLANs were allowed
- STP did not show an obvious blocking issue on the expected path
- FortiGate VLAN interface existed
- Gateway IP was configured
- Ping from Core1 to FortiGate VLAN gateway failed
- ARP/MAC learning did not behave as expected
This made the issue confusing because the basic Layer 2 and Layer 3 configuration looked correct.
5. Initial Assumptions
At first, the issue appeared to be related to one of the following:
- VLAN not allowed on trunk
- Wrong trunk configuration
- STP blocking
- FortiGate VLAN interface issue
- Wrong IP/subnet configuration
- Firewall policy issue
- ARP learning issue
- Physical/logical link issue
- FortiGate interface binding issue
However, the trunk and VLAN configuration did not show an obvious mistake.
This indicated that the problem might be related to FortiGate interface behavior rather than normal switch trunking.
6. Troubleshooting Process
The troubleshooting started from the switch side and then moved to the FortiGate interface configuration.
6.1 Switch-Side Verification
The following areas were checked on the Core switch:
- Interface status
- Trunk status
- Allowed VLANs
- STP state
- VLAN existence
- MAC address learning
- Ping toward VLAN gateway
- ARP behavior
Result:
Core switch link: Up
Trunk: Appeared correct
VLANs: Present/allowed
STP: No obvious issue on expected path
Gateway ping: Failed

This showed that the switch configuration was not the obvious root cause.
6.2 FortiGate Interface Review
The FortiGate interface configuration was then reviewed.
The internal LAN connection was using a redundant interface.
A redundant interface can include multiple physical ports, but only one member is actively used at a time for traffic forwarding.
During verification, it was found that the active/current member of the redundant interface was not the expected port toward Core1.
Instead, FortiGate had selected another member port as active.
6.3 Key Discovery
The redundant interface active member was pointing toward a port connected to the Core2 side/path, which was not ready for this traffic flow at that stage of the lab.
The expected working path was through the FortiGate port connected toward Core1.
Because the redundant interface active member was not the expected port, Core1 traffic was not reaching the FortiGate VLAN gateway correctly.
7. Root Cause
The root cause was incorrect active member selection on the FortiGate redundant interface.
The FortiGate redundant interface had selected a member port that was not part of the currently working Core1 traffic path.
As a result, the Core switch could not properly reach the FortiGate VLAN gateway, even though the switch-side trunk and VLAN configuration appeared correct.
8. Root Cause Summary
Switch trunk:
✔ Appeared correct

VLAN configuration:
✔ Present/allowed

STP:
✔ No obvious blocking issue on expected path

FortiGate VLAN gateway:
✔ Configured

Actual issue:
✘ FortiGate redundant interface active member was wrong
✘ Active member pointed toward an unready path
✘ Core1 could not reach FortiGate gateway

The problem was not simply a trunk issue.
The problem was the FortiGate redundant interface forwarding through the wrong member port.
9. Fix Applied
The FortiGate redundant interface member order was changed so that the correct interface toward Core1 became the preferred/active member.
After changing the member order, FortiGate selected the expected port as the active member.
Result:
FortiGate redundant interface active member: Correct
Core1 path toward FortiGate: Restored
Core1 learned FortiGate MAC address
Gateway ping: Successful

10. Verification After Fix
After the fix, the following verification was performed:
- Checked FortiGate redundant interface active member
- Verified Core1 trunk status
- Verified VLAN gateway reachability
- Checked ARP/MAC learning
- Tested ping from Core1 to FortiGate VLAN gateway
- Confirmed traffic was passing through the expected interface
- Verified no STP issue on the active path
Result:
Core1 to FortiGate gateway ping: Working
MAC learning: Correct
Active redundant member: Correct
Traffic path: Restored

11. Technical Explanation
A FortiGate redundant interface is not the same as a port-channel or LACP bundle.
In a redundant interface, multiple physical interfaces can be grouped together, but traffic uses one active member at a time. If the active member is not connected to the correct working path, traffic may fail even though another member is physically connected and appears available.
This can create confusion because:
- Physical interfaces may show up
- Switch trunk may look correct
- VLANs may be configured correctly
- STP may not show an obvious issue
- But FortiGate may still forward traffic using a different active member
That is why redundant interface active member verification is critical during FortiGate HA or multi-switch lab designs.
12. Useful Checks
Useful switch-side checks:
show interfaces status
show interfaces trunk
show vlan brief
show spanning-tree vlan <vlan-id>
show mac address-table
show mac address-table vlan <vlan-id>
show arp
ping <gateway-ip>

Useful FortiGate checks:
get system interface
show system interface
diagnose netlink aggregate name <interface-name>
diagnose hardware deviceinfo nic <port-name>
execute ping <gateway-or-host>
diagnose sniffer packet any "host <ip-address>" 4

FortiGate GUI checks:
Network > Interfaces
Check redundant interface members
Check current/active member
Check VLAN interfaces bound to redundant interface
Check administrative status
Check link status

13. Prevention Recommendations
To avoid similar issues in future lab or production designs:
- Do not assume all redundant interface members forward traffic at the same time
- Always check the current active member
- Verify which physical FortiGate port is actually forwarding traffic
- Match switch topology with FortiGate redundant member order
- Test gateway reachability after interface changes
- Check MAC learning from the switch side
- Use LACP/aggregate interface when active-active bundled forwarding is required
- Document expected active path clearly in the topology
- Bring up redundant paths step by step
- Verify before adding more VLANs on top of the interface
14. Key Lessons
- Redundant interface does not behave like EtherChannel/LACP.
- Interface up does not always mean the expected member is forwarding.
- Gateway failure can be caused by wrong active member selection.
- Switch trunk troubleshooting should include firewall interface behavior.
- MAC learning is a strong clue during gateway reachability issues.
- Always verify the actual forwarding path, not only the configuration.
- In labs, build and test one path before adding redundancy complexity.
15. One-Line Takeaway
The trunk was not the real issue. The FortiGate redundant interface was forwarding through the wrong active member, so Core1 could not reach the VLAN gateway.

## Commit message

```text
Add FortiGate redundant interface active member troubleshooting case study
