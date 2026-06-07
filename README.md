# llm-wiki

This is a freshly initialized, markdown-first LLM wiki inspired by Andrej Karpathy's `llm-wiki` pattern.

The wiki is intentionally small:

- `raw/`: immutable source material.
- `wiki/`: LLM-maintained markdown knowledge.
- `AGENTS.md`: the schema and operating contract for agents.

## Layout

```text
.
├── AGENTS.md
├── raw/
│   ├── assets/
│   └── sources/
└── wiki/
    ├── index.md
    ├── log.md
    └── pages/
```

## First Use

1. Add source files to `raw/sources/`.
2. Ask the agent to ingest them into `wiki/pages/`.
3. Keep `wiki/index.md` current.
4. Append every meaningful operation to `wiki/log.md`.

## Philosophy

- Prefer persistent knowledge artifacts over one-off chat answers.
- Preserve raw sources and let the wiki become a maintained synthesis layer.
- Use simple files first; add machinery only when the corpus demands it.
- Keep every update auditable and reversible through markdown history.
