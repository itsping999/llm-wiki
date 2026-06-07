# LLM Wiki Agent Contract

This file is the schema and operating manual for maintaining this LLM wiki.

## Architecture

1. `raw/` stores source-of-truth material. Treat files here as immutable after ingestion.
2. `wiki/` stores maintained markdown pages created from sources and queries.
3. `AGENTS.md` defines page schema, linking rules, and maintenance procedures.

## Page Schema

Every page in `wiki/pages/*.md` should begin with frontmatter:

```yaml
---
title: "Human readable title"
slug: "stable-slug"
type: source|concept|entity|analysis|query
created: "YYYY-MM-DDTHH:MM:SSZ"
updated: "YYYY-MM-DDTHH:MM:SSZ"
sources:
  - "raw/sources/example.md"
---
```

Use `[[stable-slug]]` for internal links.

## Ingest Procedure

1. Read source files from `raw/sources/`.
2. Create or update `type: source` pages in `wiki/pages/`.
3. Extract durable concepts/entities into their own pages when useful.
4. Update `wiki/index.md`.
5. Append a short entry to `wiki/log.md`.

## Query Procedure

1. Search `wiki/index.md` and relevant `wiki/pages/*.md`.
2. Answer from existing wiki knowledge before adding new sources.
3. Save reusable synthesis as `type: query` or `type: analysis`.
4. Record unresolved questions and evidence gaps.

## Maintenance

- Check for broken `[[links]]`.
- Notice orphan pages with no inbound links.
- Mark contradictions instead of smoothing them away.
- Prefer small incremental edits over broad rewrites.
