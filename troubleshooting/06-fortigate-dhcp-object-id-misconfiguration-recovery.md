# Case 06 --- FortiGate DHCP Object-ID Misconfiguration and Recovery

**Status:** Completed

## Context

While moving VLAN 20 DHCP service to Windows DHCP with FortiGate relay,
`show system dhcp server` showed only DHCP object `edit 1`, which
belonged to `fortilink`.

## Mistake

The numeric object was edited and its interface was accidentally changed
to `VLAN20-USERS`.

## Detection

Running `show system dhcp server` again exposed that the existing
FortiLink DHCP object had been modified.

## Fix

The object was restored to:

``` text
set interface "fortilink"
```

VLAN 20 relay was configured separately toward `HQ-DHCP01` at
`10.10.40.20`.

## Verification

DHCP DORA later completed successfully, and a Windows VLAN 20 client
received `10.10.20.101/24` with gateway `10.10.20.1`.

## Engineering Lesson

Never assume FortiGate numeric object IDs. Inspect and identify an
object by its actual interface/name/purpose before modifying it.
