# Branch Hygiene Policy

**Status**: PROPOSED — 2026-08-14
**Owner**: tuna-os (hanthor) / strategist
**Tracks**: #1530 (Branch hygiene policy), #1174 (Adoption metrics snapshot), #1363 (RFC disposition pass)

---

## Purpose

Set the rules for the `tuna-os/tunaos` repository. They cover how to name a branch, how long it lives, and when it goes stale. They also cover how to clean it up, by hand or by machine.

Branches build up when nobody manages them. This repository holds more than 97, across RFCs, agent runs, and experiments on a feature. That mass slows a contributor down, hides the live work, and makes it hard to pick a base for a PR. This policy gives each branch a life cycle you can predict, so that only live branches with an owner stay in the repository.

---

## Branch Naming & Classification

All branches pushed to `tuna-os/tunaos` must follow a recognized prefix convention and carry clear ownership:

| Branch Type | Prefix / Pattern | Max Lifetime | Owner / Responsibility | Disposition |
|---|---|---|---|---|
| **Default Branch** | `main` | Permanent | Maintainers (`tuna-os`) | Protected by GitHub Rulesets ([BRANCH-PROTECTION.md](BRANCH-PROTECTION.md)) |
| **RFC Proposals** | `rfcNNN-<slug>` | 30 days post-commit | Author / Strategist | Governed by [RFC-PROCESS.md](../RFC-PROCESS.md). Merged, abandoned, or deleted after disposition pass. |
| **Feature / Fix** | `feat/<name>`, `fix/<name>`, `docs/<name>`, `ci/<name>`, `arch/<name>` | 30 days post-commit | PR Author | Merged via PR (deleted on merge) or deleted when stale/abandoned. |
| **Agent / Automation** | `agent/<name>`, `claude/<name>`, `codex/<name>`, `auto/<name>` | 14 days post-commit | Agent / Initiator | Transient build or experiment branches. Deleted automatically on PR merge or purged when stale. |
| **Release / Stable** | `release/<version>`, `stable/<version>` | Permanent / Lifecycle | Maintainers | Long-term maintenance refs for stable releases. |

> **Fork-First Policy**: a contributor from outside, and an agent run with no push access, both work on a fork of their own. Upstream keeps its own branches for the work of the core maintainers, for release tags, and for an RFC proposal that an issue tracks.

---

## Staleness Rules & Triage Cadence

A branch goes **stale** when it gets no new commit for **30 days**, and has no open Pull Request that somebody works on. For a branch from an agent, or from automation, the limit is 14 days.

### Triage & Cleanup Rules

1. **Delete-on-Merge (Automated)**:
   The `delete-branch-on-merge` setting is on. When somebody merges a Pull Request, GitHub deletes its head branch from `tuna-os/tunaos`.

2. **The pass that decides the fate of each RFC branch**:
   At each quarterly checkpoint, the project triages the RFC branches
   (`rfcNNN-*`). [`RFC-PROCESS.md`](../RFC-PROCESS.md) gives the rules. The Q3 checkpoint of 2026-08-22 is one example (#1299 / #1363). We then delete from upstream each branch whose idea we merged, took in, replaced, or dropped.

3. **The monthly audit of stale branches**:
   One cycle each month reports [`ADOPTION-METRICS.md`](../ADOPTION-METRICS.md)
   and hygiene (#1174). It also counts the live branches across the
   authorized `tuna-os` repositories:
   - It marks a stale branch for deletion when three things hold. Nobody
     merged it. It got no commit for more than 30 days. It has no open PR.
   - A maintainer or an agent then runs a pass that deletes each branch we no longer need:
     ```bash
     git push upstream --delete <stale-branch-name>
     ```

---

## Hygiene Metrics & Reporting

The monthly snapshot of hygiene for maintainers carries two counts, beside the metrics for the release and for adoption. They are the number of branches, and the number of stale branches.

- **Target**: keep 15 live upstream branches or fewer at all times. Do not count a permanent tag or branch for a release.
- **Where it appears**: in the monthly updates on Community & Governance in the ROADMAP (#1174).
