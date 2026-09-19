# Domain Docs

How the engineering skills must read this repo's domain documentation before they examine the codebase.

## Read these first

- **`CONTEXT.md`** at the repo root, or
- **`CONTEXT-MAP.md`** at the repo root if it exists — it points at one `CONTEXT.md` per context. Read each one relevant to the topic.
- **`docs/adr/`** — read ADRs that touch the area you're about to work in. In multi-context repos, also check `src/<context>/docs/adr/` for context-scoped decisions.

If any of these files don't exist, **continue without a comment**. Don't flag the gap; don't ask anyone to write the file first. The producer skill (`/grill-with-docs`) writes each file late, when the team settles a term or a decision.

## File structure

Single-context repo (most repos):

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Multi-context repo (presence of `CONTEXT-MAP.md` at the root):

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in `CONTEXT.md`. Don't drift to synonyms the glossary explicitly avoids.

A concept that the glossary does not hold yet is a signal. Either you invent language the project does not use, and must think again, or the glossary has a real gap. Note that gap for `/grill-with-docs`.

## Flag ADR conflicts

If your output contradicts an existing ADR, say so. Do not quietly replace the decision:

> _Contradicts ADR-0007 (event-sourced orders) — but we must open it again, because…_
