# TunaOS Build Pipeline Guide

This document gives an overview of the CI/CD pipeline for TunaOS. The
pipeline builds images on schedules that do not overlap. It then publishes them
under a tag for each flavor, after the jobs that verify them pass. For the
architecture in detail, and kept current, see [`PIPELINE.md`](PIPELINE.md) and
the [Developer Guide](DEVELOPER-GUIDE.md).

## 🏗️ Architecture Overview

Most variant entry points run daily on staggered schedules. A successful build
first writes a `<flavor>-testing` manifest. The reusable workflow verifies that
candidate and then promotes it to the user-facing `<flavor>` tag. `latest` does
not represent all desktops; each flavor has its own tag.

### Variants

`.github/build-config.yml` is the source of truth for the build matrix. Do not
copy a fixed subset of this page into automation. That file declares each
variant, and which flavors, platforms, ISOs and QCOW2 images it offers. The
generated [Matrix Status](MATRIX-STATUS.md) shows the current state of
verification for each cell. The root [README](../README.md#choose-your-image)
helps a reader pick a base. It lists the published images, and the images you
can build only on your own machine.

### Flavors (4-stage DAG; source of truth: `.github/build-config.yml`)

| Stage | Flavors | Description |
|---|---|---|
| 1 | `base` | Minimal OS (required for all downstream stages) |
| 2 | `base-hwe`, `base-nvidia`, `gnome`, `cosmic`, `kde`, `niri` | HWE/nvidia base layers + desktop environments |
| 3 | `<de>-hwe`, `<de>-nvidia` (e.g. `gnome-hwe`, `kde-nvidia`) | DE layered on HWE or nvidia base |
| 4 | `gnome-nvidia-hwe` | GNOME + nvidia + HWE combined |

Flavor availability varies per variant — e.g. `marlin` omits GNOME 50 (Arch ships the latest GNOME natively).

### Hardware Enablement (HWE)

HWE is a dedicated `base-hwe` layer (stage 2), not bundled with any desktop. It provides:
- **kernel**: `coreos/fedora` via `ublue-os/akmods`
- **NVIDIA drivers**: `ublue-os/akmods-nvidia-open` using coreos-stable builds

Desktop HWE images (`<de>-hwe`) layer on the corresponding desktop image in
stage 3. NVIDIA images (`<de>-nvidia`) do the same. Both paths use
`Containerfile.overlay`; `OVERLAY_TYPE` selects the hardware layer and
`PARENT_FLAVOR` identifies the desktop parent. The stage-2 `base-hwe` and
`base-nvidia` cells stay base images that you can build on their own.

---

## 🔄 Workflows

### 1. Unified Build (`build-variant.yml`)

The shared orchestrator called by the per-variant `build-<variant>.yml` entry
points and the manual `build-flavor.yml` dispatcher.

- **Triggers**:
  - Schedule: staggered daily schedules in most per-variant entry points
  - Manual: per-variant dispatchers or `build-flavor.yml`
  - Pull request: the repository's PR workflows select the affected build and
    validation lanes; PR candidates are never promoted
- **Process**:
  1. **Matrix Generation** (`generate_matrix`): reads `.github/build-config.yml` via `yq` + `jq`, emits one matrix per stage (S1–S4)
  2. **Stage 1** (`build_base`): builds `{variant}:base` for every variant in parallel
  3. **Stage 2** (`build_stage2`): builds `base-hwe`, `base-nvidia`, and all desktop flavors (gnome, kde, niri, cosmic) — runs after all stage 1 complete
  4. **Stage 3** (`build_stage3`): builds `<de>-hwe` and `<de>-nvidia` — runs after all stage 2 complete
  5. **Stage 4** (`build_stage4`): builds `gnome-nvidia-hwe` — runs after all stage 3 complete
  6. **Artifacts** (`build_artifacts_s2`, `build_artifacts_s3`, `build_artifacts_s4`): per-stage artifact jobs build ISOs (via `just iso-tacklebox`) and QCOW2s for combo cells where `build_iso: true` / `build_qcow2: true`. Each stage's artifacts depend only on that stage's image builds.
- **Key Features**:
  - **DAG enforcement**: jobs use `needs` to hold the stages in order. Inside a stage, `fail-fast: false`
  - **Multi-platform artifacts**: `linux/amd64` and `linux/arm64` ISO/QCOW2 matrices per stage for multi-arch variants (#1378)
  - **Cosign signatures + SBOM** for every published image. A transient error causes a retry, and the backoff runs against a deadline (#1377)
  - Pull-request builds do not promote user-facing tags

### 2. Pull Request Checks

A pull request runs the repository's validation workflows, plus whichever
image build lane the rules select. It never promotes a user-facing tag. The
workflow files and the CI contract tests hold the exact rules that make that
choice. On purpose, this page does not repeat them as a fixed list of
flavors.

A change in 2026-09 deleted the old single-file `build.yml` and its
companion `generate-release.yml`. To read them, run
`git log --all -- .github/workflows/archive/`.

---

## 🛠️ Scripts & Tools

The pipeline relies on several helper scripts in the `scripts/` directory and Justfile recipes:

- **`reusable-build-image.yml`**: the shared CI job. It builds the image,
  assembles the multi-arch manifest, signs it, makes the SBOM, verifies the
  candidate, and promotes it.
- **`generate_matrix`** (in `build-variant.yml`): a `yq` and `jq` pipeline. It reads `build-config.yml` and emits one matrix, in JSON, for each stage.
- **`build-iso-tacklebox.sh`** + `just iso-tacklebox`: a bootc-to-ISO builder in Go, on `ghcr.io/tuna-os/tacklebox`. It replaces the old ISO path that used anaconda.
- **`iso-e2e.sh`**: QEMU+OVMF+KVM end-to-end harness for ISO and installed-disk
  checks; it captures screenshots and serial logs and validates readiness.
- **`dnf_retry`** (in `build_scripts/lib.sh`): retries a transient EPEL or RPM fetch failure, up to 4 times, with a backoff that doubles.

---

## 📖 How-To Guides

### How to Manually Trigger a Build
1. Go to the repository's **Actions** tab.
2. Select **Build Flavor** (`build-flavor.yml`) for a specific matrix cell, or
   select a per-variant build workflow.
3. Click **Run workflow** and choose the inputs shown by that workflow.
