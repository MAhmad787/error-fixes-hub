# VirtualBox & KVM Conflict – Quick Fix Guide

## Problem

VirtualBox cannot start VMs when KVM (Kernel-based Virtual Machine) modules are loaded.
KVM and VirtualBox both require access to Intel VT-x / AMD-V hardware virtualization, but only one can use it at a time.

## Symptoms

* VirtualBox error: "VT-x is not available"
* `dmesg` logs mention `kvm_intel` or `kvm_amd`

## Quick Fix (Temporary)

1. Check if KVM is running:

   ```bash
   lsmod | grep kvm
   ```

2. If Intel CPU:

   ```bash
   sudo rmmod kvm_intel
   ```

   If AMD CPU:

   ```bash
   sudo rmmod kvm_amd
   ```

3. Then remove the base module:

   ```bash
   sudo rmmod kvm
   ```

4. Start your VirtualBox VMs again.

## Permanent Options

* **Blacklist KVM modules** so they don’t load at boot:
  Create a file `/etc/modprobe.d/blacklist-kvm.conf` with:

  ```
  blacklist kvm
  blacklist kvm_intel
  blacklist kvm_amd
  ```

  (This disables KVM system-wide – you lose virt-manager/QEMU unless you undo this.)

* **Alternative:** Use KVM/QEMU instead of VirtualBox (faster, no conflicts).

## Notes

* Temporary removal only lasts until reboot.
* Permanent blacklisting can be undone by removing the blacklist file.
* Choose one hypervisor (VirtualBox or KVM) as your main virtualization tool.
