# Secure Boot support by variant

In short: **the Enterprise-Linux and Fedora variants boot under Secure Boot
out of the box**. Those are Yellowfin, Albacore, Skipjack, Bonito, and Redfin.
They take the shim and the kernel, both signed by the distro, from their bootc
base images.

**Every NVIDIA flavor needs you to enroll one key by hand**. Five variants sit
on a community base: Marlin, Flounder, Grouper, Sailfin, and Guppy. **None of
them supports Secure Boot out of the box today**.

"Out of the box" means: install, reboot with Secure Boot enabled, and
everything works — no firmware settings changed, no keys enrolled.

## Status table

| Variant | Base | Secure Boot out of the box? | Notes |
|---|---|---|---|
| Yellowfin | AlmaLinux Kitten 10 | ✅ Yes (standard flavors) | Alma-signed shim + kernel from the bootc base |
| Albacore | AlmaLinux 10 | ✅ Yes (standard flavors) | Alma-signed shim + kernel |
| Skipjack | CentOS Stream 10 | ✅ Yes (standard flavors) | CentOS-signed shim + kernel |
| Bonito | Fedora bootc 44 | ✅ Yes (standard flavors) | Fedora-signed shim + kernel from the bootc base |
| Bonito Rawhide | Fedora Rawhide | ✅ Yes, in principle | Rawhide is signed, but as a moving target it can hit occasional signing/SBAT gaps — expect the odd regression |
| Redfin | RHEL 10 (local build) | ✅ Yes (standard flavors) | Red Hat-signed shim + kernel — not a publicly published variant yet |
| Any `*-nvidia` flavor | EL base + ublue akmods | ⚠️ **No** — one-time key enrollment required | See [NVIDIA flavors](#nvidia-flavors) |
| Any `*-hwe` flavor | EL base | ✅ Yes, today | The HWE overlay currently keeps the signed base kernel; if it ever swaps kernels this becomes ⚠️ like NVIDIA |
| Marlin (+ CachyOS overlay) | Arch Linux | ❌ No | Unsigned kernel (stock Arch / CachyOS), no signed shim — disable Secure Boot |
| Flounder | Debian 13 (trixie) | ❌ Not out of the box | The bootcified image does not wire Debian's signed shim/kernel path; untested under SB — disable Secure Boot |
| Flounder Sid | Debian Sid | ❌ Not out of the box | Same as Flounder, on the unstable branch |
| Grouper | Ubuntu 26.04 | ❌ Not out of the box | Same as Flounder: signed-boot chain not wired in the bootcification; untested |
| Sailfin | openSUSE Tumbleweed | ❌ Not out of the box | openSUSE signs its kernels, but the bootcified image's shim path is untested — assume unsupported |
| Guppy | Gentoo | ❌ No | Source-built unsigned kernels; no signing infrastructure |

## NVIDIA flavors

Every `*-nvidia` flavor, on every variant, installs the open NVIDIA kernel
modules from Universal Blue's `akmods-nvidia-open` packages. Universal Blue
signs those modules with its own **MOK key for akmods**. Your firmware does not
trust that key. So under Secure Boot the system boots, but it holds the NVIDIA
driver back until you enroll the key once:

```bash
ujust enroll-secure-boot-key   # if available on your image
# or manually:
sudo mokutil --import /etc/pki/akmods/certs/akmods-ublue.der
```

Reboot. On the blue screen of the MOK Manager, choose *Enroll MOK*, then
*Continue*, then enter the password. For the key from Universal Blue that
password is `universalblue`. You do this once on each machine.

## Migrated systems

A move from an existing install to TunaOS (see
[`MIGRATION.md`](../MIGRATION.md)) keeps the state of your firmware. The same
table applies. On an NVIDIA flavor, enroll the MOK on the first boot after the
move.

## What "not out of the box" means practically

- **Marlin / Guppy**: disable Secure Boot in the firmware. This project
  supports no path to a signed kernel for them today.
- **Flounder / Grouper / Sailfin**: disable Secure Boot. Each of these bases
  has a signed kernel upstream. To carry the shim and the signed kernel
  through the move to bootc is therefore possible. It is future work. We
  track it for each variant, and we promise nothing.
- A row in the table above can disagree with what you see on real hardware.
  That is a defect in this document. Please file an issue.
