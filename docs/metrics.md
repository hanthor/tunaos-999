# PR / build metrics

**Status: not yet implemented.** This file states the intent honestly. It
prevents an invented answer: this repo has no pipeline for metrics today.

## What already exists

- [`.github/green-criteria.yml`](../.github/green-criteria.yml) keeps a
  snapshot of the status of each criterion over time (`status_<date>`
  fields). That is the nearest thing TunaOS has to a trend line today — see
  `docs/quality.md`.
- `.github/workflows/matrix-status.yml` and `weekly-boot-report.yml` make
  reports on build and boot at one point in time.

## What's missing

A true metric for the acceptance of a PR needs a script that reads the GitHub
API on a schedule and writes a report. Such a metric covers the time to
merge, the rate of reverts, and the rate of CI failures by category. None of
that exists yet. If the work becomes worth the cost, model it on
`scripts/gen-matrix-status.py`, which already collects build status the same
way.
