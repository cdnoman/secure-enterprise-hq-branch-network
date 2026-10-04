# Case 01 --- FortiGate Redundant Interface Active-Member Issue

**Status:** Completed

## Problem / Symptom

HQ-CORE-SW1 had VLAN 10 SVI `10.10.10.11/24`, correct trunks and
forwarding STP state, but could not ping FortiGate gateway `10.10.10.1`.
Core1 also did not learn the FortiGate VLAN 10 MAC.

## Evidence / Investigation

`diagnose netlink redundant name HQ-LAN-RED` showed both FortiGate
members up, but `current slave: port4`.

Physical design at the time: - Primary FortiGate `port2` → Core1 -
Primary FortiGate `port4` → Core2

Core2 was not yet fully ready, so the selected physical member did not
provide the usable path expected during that build stage.

## Root Cause

`HQ-LAN-RED` was actively using `port4` while the currently usable path
was through `port2` and Core1.

## Fix

The redundant interface member order was corrected so `port2` was
preferred before `port4`.

## Verification

Diagnostics showed `current slave: port2`. Core1 learned FortiGate MAC
`0009.0f09.0001` and `ping 10.10.10.1` succeeded 5/5.

## Engineering Lesson

Link-up state alone does not prove an end-to-end forwarding path. With
redundant interfaces, verify the active member and the downstream
topology behind that member.
