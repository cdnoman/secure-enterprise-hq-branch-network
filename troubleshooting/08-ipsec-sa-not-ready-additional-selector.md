# Case 08 --- IPsec Traffic Dropped Because SA Was Not Ready

**Status:** Historical --- evidence enrichment pending

## Problem / Symptom

Traffic toward an additional HQ resource did not pass through an
otherwise working site-to-site VPN.

## Evidence

FortiGate debug showed:

``` text
SA is not ready yet, drop
```

## Root Cause

The required Phase 2 SA/selector for that source/destination pair was
not ready/matched.

## Fix

Matching additional Phase 2 selector/settings were configured on both
peers.

## Verification

The additional traffic used the VPN after the matching Phase 2
configuration was established.

## Engineering Lesson

A working tunnel does not mean every subnet pair has a usable Phase 2
SA. Troubleshoot the exact flow and selector.

> Add original debug and selector screenshots when available.
