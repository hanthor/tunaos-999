# Architecture Decision Records (ADRs)

This directory holds the durable decision records for the architecture of tunaOS.

Each ADR is the record of one decision about the architecture or the process, and of why the project took it. It gives the context, the reasons, the trade-offs, and the consequences.

## Lifecycle & Governance

[`RFC-PROCESS.md`](../../RFC-PROCESS.md) governs the ADR process. [ADR 0004](0004-rfc-lifecycle.md) adopted it, and it tracks [#1093](https://github.com/tuna-os/tunaOS/issues/1093) / [#1094](https://github.com/tuna-os/tunaOS/issues/1094).

Do this when the project adopts a change to the architecture, a large rework, or a policy:
1. Write an RFC and send it for review, as [`RFC-PROCESS.md`](../../RFC-PROCESS.md) tells you.
2. After a maintainer signs off and merges it, record an ADR in `docs/adr/` with the name `NNNN-short-title.md`.
3. Add an ADR for an older decision when you must, to keep the decisions visible.

## ADR Index

| ADR | Title | Status | Date | Tracking / Context |
|---|---|---|---|---|
| [0001](0001-gdx-to-nvidia-rename.md) | Rename GDX flavor to NVIDIA | Accepted | 2026-06 | Suffix clarity (`-gdx` → `-nvidia`) |
| [0002](0002-browser-iso-builder.md) | In-browser ISO builder with zero-publish architecture | Accepted | 2026-08 | [#667](https://github.com/tuna-os/tunaOS/issues/667), [#1203](https://github.com/tuna-os/tunaOS/issues/1203) |
| [0003](0003-mkosi-co-build-poc.md) | mkosi unified image co-build investigation and PoC | Recorded | 2026-08-11 | [#999](https://github.com/tuna-os/tunaOS/issues/999) |
| [0004](0004-rfc-lifecycle.md) | RFC lifecycle policy (RFC-PROCESS.md) | Accepted | 2026-08-11 | [#1093](https://github.com/tuna-os/tunaOS/issues/1093), [#1094](https://github.com/tuna-os/tunaOS/issues/1094) |
| [0005](0005-flavor-equality.md) | Flavor equality, no primary desktop tier | Accepted | 2026-08-11 | [#1315](https://github.com/tuna-os/tunaOS/issues/1315), [#1316](https://github.com/tuna-os/tunaOS/issues/1316) |
| [0006](0006-date-based-versioning.md) | Date-based versioning + stability tiers | Accepted | 2026-08-13 | [VERSIONING.md](../../VERSIONING.md), [#274](https://github.com/tuna-os/tunaOS/issues/274) |
| [0007](0007-registry-mirror-support.md) | Registry mirror support & fallback architecture | Accepted | 2026-08-13 | RFC-009, `registry-map.yaml` |
| [0008](0008-shell-python-boundary.md) | Where shell ends and Python begins | Accepted | 2026-08-14 | [#1651](https://github.com/tuna-os/tunaOS/issues/1651) |
| [0009](0009-suite-common-namespace-ownership.md) | Clarify suite-common namespace ownership | Accepted | 2026-08-19 | [#1585](https://github.com/tuna-os/tunaOS/pull/1585) |
