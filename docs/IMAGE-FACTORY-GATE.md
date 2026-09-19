# Image Factory Completion Gate & Definition of Done (#1283)

This document sets one **Completion Gate for the image factory**, and one **Definition of Done**, for every supported TunaOS variant in `.github/build-config.yml`.

## 1. Goal

Deliver an image factory for TunaOS that works in full. In it, CI builds and publishes every supported variant. A user can install it, boot it, update it, and recover it. Fresh evidence from automation supports each of those claims.

## 2. Definition of Done (Per Variant × Flavor × Platform)

For every supported `variant × flavor × platform` cell:

- [x] **OCI Build & Publish**: OCI image builds reproducibly and publishes by immutable digest (`ghcr.io/tuna-os/<variant>@sha256:...`).
- [x] **Package & Desktop Contracts**: the cell passes the contract checks, through `verify-desktop-experience.sh` (the Desktop Contract Sweep). Those checks cover the packages and the desktop. The cell also meets the floor for completeness and parity of the desktop (#1294).
- [x] **Declared Outputs**: CI makes every declared output (OCI, ISO, QCOW2, hardware installer), and a user can find each one.
- [x] **Boot Verification**: Each declared output boots and reaches its expected desktop or service contract.
- [x] **Install-to-Disk & LUKS**: Complete install-to-disk, reboot, and first boot verified (`luks-e2e.yml`).
- [x] **Lifecycle Stream Operations**: each published stream passes the checks on bootc `update`, on `rebase`, and on `rollback` (`bootc-lifecycle.yml`).
- [x] **Supply-Chain Verification**: the build attaches three items and then verifies them, and a gate can enforce all three. They are keyless signatures from Cosign v3, SBOMs in SPDX, and attestations of provenance ([`VERIFY-ARTIFACTS.md`](VERIFY-ARTIFACTS.md)).
- [x] **Fresh Evidence**: Automated evidence is fresh, linked, and required for stable promotion ([MATRIX-STATUS.md](MATRIX-STATUS.md)).
- [x] **Failure Visibility**: nothing hides a failure on a scheduled run. Not the status of the whole workflow, and not a quiet skip.
- [x] **Catalog & Artifact Currency**: User-facing catalog, documentation, release assets, and checksums agree.

## 3. Integrated Factory-Wide Gates

1. **Lifecycle Ledger & Ledger Gate (`#1278`, `#1283`)**:
   - `scripts/gen-matrix-status.py` writes `docs/MATRIX-STATUS.md` again. That file gives the state of LUKS E2E, of Desktop Contract, of Bootc Lifecycle, and of Installer Smoke.
2. **Installer Coverage (`#1279`)**:
   - `scripts/e2e-installer-gui-checks.sh` derives the test coverage of each installer frontend from the capabilities that the cell declares.
3. **Bootc Update/Rebase/Rollback (`#1280`)**:
   - `.github/workflows/bootc-lifecycle.yml` validates stream updates, variant/desktop rebases, and rollback recovery.
   - That validation is client-side (a host already on the bad image can
     `bootc rollback`). Repointing the published bare tag away from a bad
     digest so hosts that haven't updated yet stop receiving it is a
     separate, manual step: see
     [../runbooks/rollback-a-bad-image-promotion.md](../runbooks/rollback-a-bad-image-promotion.md).
4. **Browser & On-Demand ISO Parity (`#1281`)**:
   - Browser ISO generator (`publish-iso-groups.yml`) aligns with on-demand tacklebox builds.
5. **Supply Chain Enforcement (`#1187`, `#1193`)**:
   - Keyless OIDC signatures from Cosign, SBOM attestations in SPDX, and bundles that verify an ISO ([`VERIFY-ARTIFACTS.md`](VERIFY-ARTIFACTS.md)).
6. **Release Currency & Lifecycle Admission (`#1254`, `#1175`, `#1196`, `#1270`)**:
   - Strict admission criteria for new variants, desktop flavors, and hardware/kernel profiles (T2/Asahi/HWE) before matrix expansion (#1270).
   - Scheduled release currency and flavor parity enforcement across all published flavors (#1254).
7. **Desktop Parity & Completeness Gate (`#1294`)**:
   - Verification of desktop completeness and minimum package/size floors across non-RPM and RPM bases to prevent thin-desktop releases.
8. **Install & Hardware Verification (`#979`, `#1099`, `#989`, `#777`, `#781`)**:
   - Runs that qualify the hardware: Apple Silicon under Asahi (`scripts/verify-asahi-image.sh`), and Intel T2.

## 4. Current Matrix Verification References

- **LUKS E2E & Desktop Contract Ledger**: [MATRIX-STATUS.md](MATRIX-STATUS.md)
- **Pipeline & Build Architecture**: [PIPELINE.md](PIPELINE.md)
- **Artifact Verification Guide**: [VERIFY-ARTIFACTS.md](VERIFY-ARTIFACTS.md)
