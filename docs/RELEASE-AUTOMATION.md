# Release Automation — Q4 2026

**Tracker**: [tunaOS#1186](https://github.com/tuna-os/tunaos/issues/1186)<br>
**Milestone**: Q4 2026 — Mature<br>
**Owner**: ci-maintainer

This is the definition of done for the Q4 goal on release automation. It
turns the date-based tags in [`VERSIONING.md`](../VERSIONING.md) into a release
process. CI can see what that process misses, and anyone can verify what it
publishes.

## Acceptance criteria

- **Cadence**: on a schedule, the workflow tries the daily date-based
  release for each admitted stream. A day with no build is legitimate and may
  stay green. But a stale Releases page must fail the workflow after the
  freshness window of eight days that `MAX_RELEASE_AGE_DAYS` sets.
- **Asset completeness**: every release that is not a dry run must hold at
  least one uploaded asset. It must also hold an SBOM in SPDX format. After
  it creates the GitHub Release, the workflow reads that release back through
  the API. An action that reports success can therefore not hide an empty
  release.
- **Fail-on-drop**: when a build ran but the job finds no usable SBOM, the
  release job fails (`reason=no-sbom`). This tells a dropped release apart
  from a legitimate day with no build, and keeps the signal that #1147 needs.
- **Traceability**: the release tag follows
  `<stream>-<YYYYMMDD>`, and the release summary records the stream, tag,
  source build run, and SBOM package count.

The implementation lives in
`.github/workflows/generate-changelog-release.yml`. Stream coverage and
release cadence parity remain tracked separately where a stream is not yet
admitted to the scheduled matrix.
