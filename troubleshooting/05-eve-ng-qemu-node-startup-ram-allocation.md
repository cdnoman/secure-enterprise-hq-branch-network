# Case 05 --- EVE-NG QEMU Node Startup Failure Due to RAM Allocation

**Status:** Completed

## Problem / Symptom

`HQ-UTIL01` would not start. Web and Mail server nodes also appeared
unable to start during the same check.

## Investigation

Because multiple unrelated QEMU nodes were affected, host resource
pressure was considered before guest networking or OS troubleshooting.

## Root Cause

Excessive RAM allocation/resource pressure in the EVE-NG lab.

## Fix

VM RAM allocation was corrected/right-sized.

## Verification

`HQ-UTIL01` started successfully.

## Engineering Lesson

In a large virtual enterprise lab, resource sizing is part of the
architecture. When several unrelated nodes fail to start, check host
RAM/CPU/storage pressure early.
