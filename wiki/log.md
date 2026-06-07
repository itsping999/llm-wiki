# Wiki Log

## 2026-06-07 00:00 UTC | bootstrap
- scope: initialize Karpathy-style llm-wiki scaffold
- changed: created raw/wiki structure and agent contract
- unresolved: none
- next: add first source under raw/sources and run ingest loop

## 2026-06-07 06:23 UTC | ingest handbooks
- scope: import handbook source snapshot from `~/handbooks`
- changed: copied 17 markdown handbooks and 38 image assets into `raw/sources/handbooks`; generated 17 source pages; updated `wiki/index.md`
- unresolved: user requested `~/handbook`, but local path exists as `~/handbooks`; import used `~/handbooks`
- next: extract concept/entity pages when queries need cross-handbook synthesis
