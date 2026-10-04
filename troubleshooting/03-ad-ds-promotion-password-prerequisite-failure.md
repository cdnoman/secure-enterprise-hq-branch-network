# Case 03 --- AD DS Promotion Password Prerequisite Failure

**Status:** Completed

## Problem / Symptom

The prerequisite check for promoting `HQ-DC01` as the first domain
controller failed.

## Root Cause

The local Administrator password did not meet the required password
policy.

## Fix

A compliant password was set with:

``` cmd
net user Administrator *
```

The promotion prerequisite check was then repeated.

## Verification

`HQ-DC01` was successfully promoted as the first AD DS/DNS server for
`noman.com`. DNS later resolved `noman.com` and `HQ-DC01.noman.com` to
`10.10.40.10`.

## Engineering Lesson

Validate basic Windows prerequisites---hostname, static addressing, DNS
planning and Administrator credentials---before AD DS promotion.
