# Case 02 --- Windows Server Duplicate SID Domain-Join Failure

**Status:** Completed

## Problem / Symptom

`HQ-DHCP01` failed to join `noman.com`. Windows reported that the SID of
the domain being joined was identical to the SID of the machine.

## Root Cause

The server was cloned from the same non-generalized Windows
image/template, causing duplicate machine identity/SID information.

## Fix

On `HQ-DHCP01`, Sysprep was run using **OOBE + Generalize + Shutdown**.
After reboot/OOBE, hostname, static IP and DNS settings were restored
and the domain join was attempted again.

## Verification

`HQ-DHCP01` successfully joined `noman.com`.

## Engineering Lesson

Generalize cloned Windows Server images before deploying them as
separate domain members.
