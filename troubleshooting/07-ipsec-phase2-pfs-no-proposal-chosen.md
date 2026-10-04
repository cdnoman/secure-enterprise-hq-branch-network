# Case 07 --- IPsec Phase 2 Proposal / PFS Mismatch

**Status:** Historical --- evidence enrichment pending

## Problem / Symptom

In an earlier HQ↔Branch VPN build, Phase 1 could establish while Phase 2
negotiation failed with proposal-related errors.

## Root Cause

Phase 2 cryptographic/PFS settings were not aligned between the VPN
peers.

## Fix

Phase 2 parameters were matched on both sides.

## Verification

The Phase 2 SA established after the settings were aligned.

## Engineering Lesson

When Phase 1 is up but data SAs fail, compare Phase 2 proposals, PFS,
selectors and lifetimes before changing unrelated routing or firewall
configuration.

> Add original debug screenshots/output when available.
