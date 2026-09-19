# Publishing workflow-file fixes

**Owner:** repository maintainers and the GitHub App owner

This runbook covers the GitHub error:

```text
refusing to allow a GitHub App to create or update workflow
`.github/workflows/...` without `workflows` permission
```

## What the error means

GitHub protects every file under `.github/workflows/` against a write. The
ordinary `contents: write` access of the token is not enough. That one
installation of the GitHub App must also hold the **Workflows: Read and
write** permission, at the level of the App. No `permissions:` block in a
workflow can grant it. A change to how the repository protects its branches
does not get around it either.

The failure happens during `git push`, before a pull request exists. A normal
non-workflow commit may push successfully with the same token, which can make
the installation look healthy when it is not.

## Restore the hive App path

Two parties may need to act, in this order:

1. The App owner opens the App settings, edits **Repository permissions →
   Workflows**, selects **Read and write**, and sends the change.
2. An administrator of the organization opens the settings for the installed
   apps. There they select the hive App, and approve the request that waits
   for them. GitHub shows the approval banner on that page when somebody has
   installed the App again, or has reset its permissions. In that case GitHub
   does not apply the change to the App on its own.
3. Try again with a small branch that fixes a workflow, and that somebody has
   already reviewed. Verify that the push reaches the fork, and that the new
   PR holds the diff of the workflow. Do not use a test commit on `main`.

If the organization refuses the request, record that decision in the issue
that tracks it. Then send each change to a workflow through an
approved route. That route is a person, or an installation of an App, with
write access to `workflows`. Keep the patch in the PR, or in the
discussion on the issue, until you know that the other route works.

## Triage checklist for a rejected push

- Preserve the complete error, repository, branch, and affected workflow path
  in the issue.
- Confirm the branch contains only the intended workflow change with
  `git diff upstream/main...HEAD -- .github/workflows/`.
- Push a branch that touches no workflow, and see whether that succeeds. A
  push that works points at the permission on the App. A push that fails
  points at the authentication of the fork, or at the protection of the
  branch.
- Check the permission on the App. Then check, apart from it, the approval on
  the installation in the organization. A change to only one of the two can
  leave the installation in a state that waits.
- Link the replacement PR. Close the incident only after the PR shows the
  workflow file, and its checks start.

## Current incident references

- [#1557](https://github.com/tuna-os/tunaos/issues/1557) — hive App missing
  `workflows` permission.
- [#1390](https://github.com/tuna-os/tunaos/issues/1390) — a fix to the
  checkout in Catalog Facts, which later went through
  [PR #1484](https://github.com/tuna-os/tunaos/pull/1484).
- [#1430](https://github.com/tuna-os/tunaos/issues/1430) — a fix to the matrix
  in Bootc Lifecycle, which later went through
  [PR #1519](https://github.com/tuna-os/tunaos/pull/1519).

This runbook gives the path back to access. It does not claim that a commit in
the repository can grant a permission to an App.
