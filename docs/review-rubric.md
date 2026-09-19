# PR review rubric

What a reviewer, human or agent, checks before approval of a PR here, beyond a
green CI:

1. **The checks ran, and they ran on the real change**. `CONTRIBUTING.md`
   makes `just fix && just check` and `just test` mandatory. Verify that the
   PR's CI ran them on the current head, not on an older commit.
2. **Effect on the green criteria**. Does the change touch the build, the
   desktop, or the boot? If it does, find out whether it moves the status of
   any cell in `.github/green-criteria.yml`. A PR that quietly breaks a
   `blocking` criterion (see `docs/quality.md`) must say so. Nobody must have
   to find it later, in a nightly sweep.
3. **The scope matches the issue**. Take the smallest change that meets the
   acceptance criteria of the linked issue. `CONTRIBUTING.md` gives the loop,
   from fork to PR. Flag any unrelated change in the same PR.
4. **Known landmines**. Check the PR against the gotchas that `AGENTS.md`
   records. One example: know your base before you make an argument about its
   packages. A fix can look correct on its own and still repeat a mistake that
   somebody made, and recorded, once before.
5. **Diagnose a CI failure; never silence it**. A check that fails needs a
   fix, or a clear statement of why it has no relation to this PR. It can be
   a fault that was there before, or a fault in the infrastructure. Never
   accept a skip, a disable, or a retry until green, with no root cause.
