# Branch Hygiene & Lifecycle Policy

**Status**: Published Policy  
**Tracks**: [#1530](https://github.com/tuna-os/tunaOS/issues/1530) (Branch hygiene policy missing)  
**Applies to**: `tuna-os/tunaos` and all active repositories in the `tuna-os` organization.

---

## 🎯 Purpose

The `tuna-os` organization grows, across workflows with many agents and
across contributions from the community. A clean namespace of branches is
therefore necessary. It does four things:
- It makes it easier for a contributor from outside to find the base branch that is live.
- It keeps stale feature branches and stale experiment branches out of the repository.
- It puts a clear time limit on the decision about each RFC on the architecture.
- It keeps the metrics of the repository, and the record of its status, clean.

---

## 📐 Branch Naming Conventions

All branches created in `tuna-os` repositories must follow standard prefixes matching their intent:

| Prefix | Purpose | Owner & Lifecycle |
|---|---|---|
| `main` | Production / default branch | Protected by rulesets; persistent |
| `feat/*` / `fix/*` | Feature development & bug fixes | Contributor/Agent; deleted upon merge |
| `ci/*` / `arch/*` | CI workflows & architectural updates | Core/Agent; deleted upon merge |
| `strategy/*` / `outreach/*` | Roadmap, planning, and community docs | Core/Agent; deleted upon merge |
| `rfc/rfcXXX-*` | Architecture RFC proposals | Proposer; evaluated at checkpoint (#1363) |
| `renovate/*` / `dependabot/*` | Automated dependency updates | Bot; auto-pruned upon merge/supersede |

---

## 🔄 Branch Lifecycle Rules

### 1. Feature & Fix Branches (`feat/*`, `fix/*`, `ci/*`, `arch/*`, `strategy/*`)
- **Delete on Merge**: the repository settings enforce `delete_branch_on_merge: true`. GitHub deletes every merged PR branch from the remote.
- **Stale Branch Limit (30 Days)**: the monthly triage examines each unmerged `feat/*` or `fix/*` branch. Any such branch with no commit for more than 30 days comes up there. If its owner has left it, we delete it or archive it.

### 2. RFC Branches (`rfc/rfcXXX-*`)
- **Checkpoint Disposition**: the community decides the fate of each RFC branch at a scheduled checkpoint. One example is the Q3 2026 checkpoint, #1363. These are the branches for a proposal about the architecture, `rfc001` through `rfc009`.
- **The two outcomes**:
  - **Accepted**: we merge it into `main`, for example into `docs/adr/` or `docs/`, and then delete the remote branch.
  - **Rejected / Superseded**: we close it and delete it from the remote. The issue tracker and the ADR index keep the record.

### 3. Experimental & Spike Branches (`exp/*`, `diag/*`)
- **Temporary Scope**: Diagnostic and spike branches must have a designated owner (human or agent lane).
- **14-Day Limit**: within 14 days, the owner must settle a spike branch, turn it into a PR, or delete it.

---

## 🧹 Org-Wide Triage & Metrics Integration

1. **Monthly Branch Triage**:
   - Before a release, and before a checkpoint, a sweep counts every branch on the remote.
   - We prune a branch that goes past a limit above, unless a live issue tracks it.
2. **Adoption metrics**:
   - The monthly snapshots in `ADOPTION-METRICS.md` count the live branches across the organization, as a metric of hygiene (#1174).
