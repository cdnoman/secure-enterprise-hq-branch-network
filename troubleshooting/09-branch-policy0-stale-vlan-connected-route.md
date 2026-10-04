# Case 09 --- Branch Traffic Hit Policy 0 Because of Stale Connected Routes

**Status:** Historical --- evidence enrichment pending

## Problem / Symptom

Branch-to-HQ traffic hit FortiGate policy 0 instead of following the
intended routed Core/IPsec path.

## Investigation

Old Branch FortiGate VLAN interfaces were still present and installed
more-specific connected routes.

## Root Cause

Stale connected routes from an earlier design overrode the intended
routed Branch Core path.

## Fix

Obsolete VLAN interfaces/dependencies were disabled or removed.

## Verification

Traffic returned to the intended routed Core/IPsec path.

## Engineering Lesson

After changing Layer-3 ownership, remove stale interfaces and connected
routes. Route selection can be correct according to configuration while
still being wrong according to the intended design.
