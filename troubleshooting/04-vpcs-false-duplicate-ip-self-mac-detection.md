# Case 04 --- VPCS False Duplicate-IP / Self-MAC Detection

**Status:** Completed / Emulator-specific behavior isolated

## Problem / Symptom

A VPCS client in VLAN 20 completed DHCP DORA but did not retain its
leased address. `show ip` returned `0.0.0.0/0`.

## Evidence

DHCP debug showed: - Discover - Offer `10.10.20.100` - Request
`10.10.20.100` - ACK `10.10.20.100`

The ACK supplied gateway `10.10.20.1`, DNS `10.10.40.10`, domain
`noman.com`, and DHCP server `10.10.40.20`. Windows DHCP Manager also
showed a valid lease for `VPCS1.noman.com`.

VPCS then reported:

``` text
00:50:79:66:68:09 use my ip 10.10.20.100
```

The detected MAC was the VPCS client's own MAC. It then cleared its
IP/gateway. A manual static assignment triggered the same self-MAC
duplicate detection.

## Isolation

The ASW1↔ASW2 link was temporarily shut to test a redundant-L2-path
hypothesis. The symptom remained, so that hypothesis was rejected and
the link was restored.

## Verification

A Windows 7 VLAN 20 client successfully received `10.10.20.101/24`,
gateway `10.10.20.1`, DNS suffix `noman.com`, and successfully pinged
`10.10.20.1`.

## Engineering Lesson

Validate an apparent network failure with a second endpoint type before
redesigning a working network around an emulator-specific anomaly.
