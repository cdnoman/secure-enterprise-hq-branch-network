# Case 10 --- HSRP Virtual-MAC Forwarding Issue in Cisco IOL

**Status:** Historical / emulator-specific workaround

## Problem / Symptom

The Branch distribution layer used HSRP, but the EVE-NG IOL environment
showed unexpected forwarding behavior involving the HSRP virtual MAC.

## Workaround

`standby use-bia` was used to work around the IOL virtual-MAC dataplane
behavior. An HSRP peer issue on the distribution interconnect also
required an emulator-specific IGMP-snooping workaround.

## Verification

HSRP gateway forwarding worked in the lab after the workaround.

## Engineering Lesson

Clearly label emulator-specific workarounds. A lab workaround should not
automatically become a production design recommendation.

> Add original `show standby` and ping evidence when available.
