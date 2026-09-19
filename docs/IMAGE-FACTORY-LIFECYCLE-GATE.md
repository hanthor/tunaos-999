# Image Factory Lifecycle Coverage & Required Gate (#1278)

This document establishes the machine-enforced requirement for full lifecycle coverage across all variant, flavor, and platform cells declared in `.github/build-config.yml`.

---

## 1. Machine-Enforced Lifecycle Ledger

`scripts/gen-matrix-status.py` makes the lifecycle ledger. For each cell, it computes the status as it runs, and it follows how far the matrix obeys the rules. It covers all 13 declared variants and 142 combinations of flavor.

Each cell in the ledger records evidence across the full definition of done:
1. **Container Build & Published Digest**: OCI image build reproducibility and immutable digest (`ghcr.io/tuna-os/<variant>@sha256:...`).
2. **Desktop & Package Contracts**: `verify-desktop-experience.sh` contract suite.
3. **Declared Output Artifacts**: OCI, ISO, QCOW2, and hardware installer formats.
4. **Boot Verification**: Automated boot checks for each generated output format.
5. **Installer & LUKS E2E**: partitions on the disk, LUKS encryption, and the checks on the first boot (`luks-e2e.yml`).
6. **Lifecycle Operations**: `bootc` stream update, variant rebase, and rollback validation (`bootc-lifecycle.yml`).
7. **Supply Chain Provenance**: Cosign keyless signatures, SPDX SBOM attestations, and provenance bundles (`docs/VERIFY-ARTIFACTS.md`).
8. **Freshness & Traceability**: Direct links to source workflow runs and timestamp currency checks.

---

## 2. Capability & Waiver Rules

- **No Implicit Skips**: every cell that `.github/build-config.yml` declares must satisfy the capabilities the gate asks for. If it cannot, it must record a waiver that is still live.
- **Waiver Expiry**: a waiver for an experimental or beta cell needs a named owner and a date of expiry. CI fails on a waiver after that date.
- **CI Gate Integration**: `.github/workflows/matrix-status.yml` holds the structure and the capabilities to account in each PR (`--check-structure`). Nobody can add a variant, or drop a cell, without a record, and then go around the release gates.
