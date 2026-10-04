# Case 14 --- Windows 7 VirtIO Disk Not Detected in EVE-NG

**Status:** Completed

## Problem / Symptom

During Windows 7 x86 deployment, the installer could not use the
VirtIO-backed disk.

## Investigation

EVE/QEMU image naming was checked. The ISO needed to be `cdrom.iso`. The
VirtIO disk required a storage driver that Windows 7 did not have
available by default.

## Root Cause

Missing VirtIO storage-driver support during Windows 7 installation.

## Fix

The virtual disk was changed to `hda.qcow2`. The working template used
the `tpl(4.1.0)` QEMU option; the tested QEMU 2.0.2 option did not work
for this build.

## Verification

The Windows 7 installer could see/use the disk and installation
proceeded.

## Engineering Lesson

Guest OS driver support must influence virtual hardware choices. Image
naming and QEMU template selection are also part of reproducible EVE-NG
deployment.
