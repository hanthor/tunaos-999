# Community Governance Model

**Status**: Active | **Tracks**: #1168 (Q4 goal — Community governance model)

## Roles and Authority

1. **Contributor**: Submits PRs, opens issues.
2. **Reviewer/Triage**: Can review PRs, label issues, and run initial triage.
3. **Maintainer**: Has review and merge authority, sets project direction.
4. **Project Lead**: Final escalation point for lazy-consensus disputes.

## Decision Process

We use a **lazy-consensus** model. A contributor opens a PR or an RFC. If nobody objects in 72 hours, the project approves it, on condition that CI passes and that it obeys our guidelines. If somebody objects, the people involved must settle the objection in discussion. If they cannot agree, the Project Lead settles the dispute.

## RFC Lifecycle Integration

Major architectural changes must go through an RFC process.
- Draft an RFC document.
- Open a PR for the RFC.
- Lazy-consensus applies for adoption.

## Per-Repo CODEOWNERS Policy

Each repository must have a `CODEOWNERS` file. That file names who holds review and merge authority over each path. Nobody may merge a code change until an owner of that code approves it.
