# Quality dashboard

TunaOS has no separate app for a quality dashboard. The quality signal lives
in [`.github/green-criteria.yml`](../.github/green-criteria.yml), with
[`docs/GREEN-CRITERIA.md`](GREEN-CRITERIA.md) as its companion in prose. That
file is the source of truth for what "green" means for one cell, that is, for
one pair of variant and flavor. `tests/test_green_criteria.py` keeps it
honest.

Each criterion in that file records:

- `enforcement`. `blocking` means no cell can go green without it.
  `advisory` means the project measures it and reports it, but it stops
  nothing yet. `unimplemented` means no automatic check exists yet.
- a `status_<date>` history. It makes the progress since the project raised
  the bar (`raised_on: 2026-08-17`) a measurement, not a memory.
- which workflow asserts it (`asserted_by`). A criterion therefore always
  traces back to a real check that somebody can run, and not to a hope.

One composite rule makes the list mean something. A cell goes green only when
every `blocking` criterion holds a positive and *current* result. Evidence
that is absent, skipped, or stale does not satisfy a criterion. To read the
file for one cell, see `skills/check-green-criteria/SKILL.md`.
