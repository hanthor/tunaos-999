# Testing Guide

TunaOS uses a test harness on QEMU, end to end, to prove that an ISO image boots correctly and reaches the live desktop.

## Publish Gating

The pipeline publishes nothing for a user until a boot check passes. It obeys
the same promotion model as Bluefin, from the `-testing` tag to the stable one:

| Artifact | Gate | Where |
|---|---|---|
| GHCR image `:<flavor>` | Manifest is pushed as `:<flavor>-testing`; a **boot gate** (`verify_boot` job) installs it to a qcow2 with bootc, boots it in QEMU (`scripts/iso-e2e.sh --disk`), and only then the promote job writes `:<flavor>`, `:<flavor>-YYYYMMDD`, and per-arch tags. `base*` flavors skip the boot gate (no desktop) and promote after manifest. | `reusable-build-image.yml` |
| ISOs on R2 / GitHub Releases | ISO is built, boot-verified in QEMU (`scripts/iso-e2e.sh`, readiness marker **or** screenshot-sanity fallback), and only uploaded if the gate passes. | `reusable-build-artifacts.yml`, `publish-iso-groups.yml` |
| PRs | Build + QEMU boot verification of the locally built image (amd64). | `reusable-build-image.yml` |

The fallback to a screenshot check exists for one reason: the EL10 bootc
kernels ship `CONFIG_SERIAL_8250=m`, so a readiness marker often never
reaches the serial console. A framebuffer that `-vga virtio` captures, and
that is not blank, counts as a boot. A black or absent one fails the gate.

Run the same gates locally:

```bash
just verify-disk yellowfin.qcow2      # boot-gate a disk image
./scripts/iso-e2e.sh some.iso         # boot-gate an ISO
```

Two workflows take the screenshots. `weekly-desktop-screenshots.yml` commits
a real capture of the desktop, for each pair of variant and DE, to
`docs/images/desktops/`. `installer-screenshots.yml` drives the GUI installer,
commits the captures of that flow to `docs/images/installer/`, and then boots
the disk the walkthrough installed to check it.

Each base-DE pair has a LUKS E2E WebM.

See the [latest LUKS install timelapses](https://tuna-os.github.io/tunaOS/e2e/luks/latest/).
HWE and NVIDIA overlays stay in the per-cell artifacts. The source run links
them.

## ISO End-to-End Tests

The `scripts/iso-e2e.sh` script boots a TunaOS live ISO in QEMU under OVMF (UEFI). It waits for the live environment, captures screenshots, and collects the serial logs.

### Running Locally

```bash
# Download a pre-built ISO
wget https://download.tunaos.org/live-isos/yellowfin-gnome-latest.iso

# Run the e2e test
./scripts/iso-e2e.sh yellowfin gnome ./yellowfin-gnome-latest.iso
```

Requirements:
- `qemu-system-x86_64` with KVM support
- `qemu-utils` for `qemu-img`
- `socat` for QEMU monitor communication
- At least 4GB RAM available for the VM

### What Gets Tested

The harness validates:
1. **Boot success** — ISO reaches the live environment within 90 seconds
2. **Desktop readiness** — `gdm.service` or equivalent display manager is active
3. **No important failure** — no `Failed to start` in the systemd journal
4. **Screenshot capture** — visual confirmation of the desktop
5. **Serial logs** — full boot output for debugging

### CI Integration

The `.github/workflows/iso-e2e.yml` workflow runs automatically:
- **On PRs** that change a build input (`Containerfile*`, `build_scripts/**`, `live-iso/**`, `system_files*/**`)
- **Weekly** on a schedule for regression detection
- Posts PR comments with screenshots and serial log summaries

### Test Artifacts

The harness uploads its output as artifacts of GitHub Actions:
- `screenshot.png` — Desktop screenshot via QEMU monitor
- `serial.log` — Full boot serial console output

## Test Files

| File | Purpose |
|---|---|
| `tests/anaconda-ks.cfg` | Kickstart configuration for automated Anaconda install testing |
| `tests/lima-template.yaml` | Lima VM template for macOS testing |
| `tests/live-iso-verify.yaml` | Live ISO verification manifest |
| `scripts/iso-e2e.sh` | Main QEMU-based end-to-end test runner |
| `.github/workflows/iso-e2e.yml` | CI workflow for automated ISO testing |
| `tests/functional/run.sh` | Tier-1 functional checks for a booted image (per-desktop SSH assertions, tuna-os/tunaos#576) |
| `tests/bats/test_functional_run.bats` | Unit tests for the functional-check dispatcher (stubbed systemctl/bootc/flatpak) |

## Functional Checks (per-image, per-desktop)

The boot gate proves that an image *boots*. `tests/functional/run.sh` proves
that the features a user needs are present, and that they work on the
**live** system (tuna-os/tunaos#576). It runs over SSH against an image that
has booted: a corral VM, a gate VM, or a local install. It emits `ok` and
`not ok` lines in the TAP style, and its exit code is the number of
failures.

It checks these things:

- The system is up and has reached `graphical.target`.
- No unit has failed, apart from the units on the allowlist for VM noise,
  `libstoragemgmt` and `mcelog`.
- The display manager of the desktop is active.
- The session binary of the desktop, and an entry for that session, are both
  present.
- `bootc status` is healthy.
- The image holds the configuration for Flathub.
- When you name a variant, it also checks `image-info.json` and the brand
  marks in the installer's `recipe.json`.

```bash
# Against a corral VM running a booted image. `corral ssh` takes at most one
# positional arg (the VM name) — its -c flag is a SINGLE command string, so
# the desktop/variant args go inside that string, not after the redirection.
corral ssh <vm> -u root -c 'bash -s gnome yellowfin' < tests/functional/run.sh

# Or copy it in and run inside the guest
scp tests/functional/run.sh root@<guest>:/tmp/ && ssh root@<guest> bash /tmp/run.sh kde yellowfin

# Composefs variant (e.g. grouper): opt in to the composefs assertion
FUNCTIONAL_EXPECT_COMPOSEFS=1 tests/functional/run.sh gnome grouper
```

Three callers run this dispatcher on their own, after their own checks on
`graphical.target` and on the display manager: `just boot-gate <variant>
[flavor]`, `scripts/boot-gate.sh`, and the `verify_boot` step in CI
(`reusable-build-image.yml`). In `verify_boot` the result is advisory. The
comment in `boot-gate.sh` maps each overlay suffix to a desktop name. It also
explains why a `*-nvidia` or `*-hwe` flavor still resolves to a bare desktop
name that `run.sh` understands.

Run the dispatcher's unit tests with the rest of the suite:

```bash
bats tests/bats/test_functional_run.bats
```

## Writing New Tests

For desktop environment changes:
1. Build the ISO: `sudo just iso yellowfin gnome local`
2. Run the e2e harness: `./scripts/iso-e2e.sh yellowfin gnome .build/iso/yellowfin-gnome.iso`
3. Verify the screenshot shows the expected desktop
4. Check serial logs for any unexpected failures

## Troubleshooting

| Symptom | Likely Cause |
|---|---|
| ISO doesn't boot | Missing KVM support; try adding `--no-kvm` to the QEMU command |
| Timeout waiting for desktop | Desktop environment failed to start; check serial logs |
| `qemu-img` not found | Install `qemu-utils` package |
| Screenshot is blank | **Not necessarily a failure.** Under plain virtio-vga (no render node) the guest paints with Mesa's llvmpipe software rasteriser, and first paint can trail the serial markers by a minute or more on a 2-4 vCPU runner (tunaOS#581). The gates key off the serial markers (`TUNAOS_DESKTOP_CONTRACT_OK` / readiness marker); `iso-e2e.sh` waits for the framebuffer to actually paint before the evidence screenshot (`wait_for_paint`, bounded by `TBOX_E2E_PAINT_TIMEOUT`, default 120s) instead of a fixed sleep. A still-blank capture after that cap means the image genuinely never painted — check `serial.log` for whether the display manager started |
