# Enterprise Lab Troubleshooting Case Studies

This directory preserves troubleshooting incidents from the **Secure
Enterprise HQ--Branch Network Lab**.

## Status Key

-   **Completed** --- enough retained evidence exists for a useful case
    study.
-   **Historical** --- root cause/fix is known; original screenshots or
    CLI can be added later.
-   **Placeholder** --- incident is preserved so it is not forgotten,
    but exact evidence should be recovered before publishing a detailed
    narrative.

## Case Index

  \#   Case                                                 Status
  ---- ---------------------------------------------------- -------------
  01   FortiGate Redundant Interface Active-Member Issue    Completed
  02   Windows Server Duplicate SID Domain-Join Failure     Completed
  03   AD DS Promotion Password Prerequisite Failure        Completed
  04   VPCS False Duplicate-IP / Self-MAC Detection         Completed
  05   EVE-NG QEMU Node Startup / RAM Allocation            Completed
  06   FortiGate DHCP Object-ID Misconfiguration Recovery   Completed
  07   IPsec Phase 2 Proposal / PFS Mismatch                Historical
  08   IPsec SA Not Ready / Additional Selector             Historical
  09   Branch Policy 0 / Stale Connected Route              Historical
  10   Branch HSRP IOL Virtual-MAC Issue                    Historical
  11   Branch Core Duplicate-IP / SVI Conflict              Placeholder
  12   HQ Access Redundant Uplink / STP Recovery            Placeholder
  13   LACP Passive/Passive Port-Channel Failure            Placeholder
  14   Windows 7 VirtIO Disk Not Detected                   Completed
  15   Windows Firewall Blocking IPsec Validation           Placeholder
  16   EVE-NG Link-State / Failover Testing Limitation      Placeholder
  17   FortiGate Interface Dependency During Cleanup        Placeholder

## Documentation Standard

Each completed case should contain: **Problem/Symptom →
Evidence/Investigation → Root Cause → Fix → Verification → Engineering
Lesson**.

Do not invent missing details. Add original screenshots, CLI output,
debug output, and configuration evidence as they are recovered.
