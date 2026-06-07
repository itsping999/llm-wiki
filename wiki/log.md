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

## 2026-06-07 06:29 UTC | query redis sentinel configuration
- scope: answer Redis Sentinel configuration from `[[handbook-redis]]`
- changed: added `[[query-redis-sentinel-config]]` with a reusable Sentinel setup checklist and updated `wiki/index.md`
- unresolved: exact deployment IPs, Redis version, ACL policy, and Docker/NAT topology are environment-specific
- next: adapt the template values before applying to a live Redis deployment

## 2026-06-07 06:37 UTC | query mysql knowledge map
- scope: summarize MySQL knowledge coverage from `[[handbook-mysql]]`
- changed: added `[[query-mysql-knowledge-map]]` and updated `wiki/index.md`
- unresolved: current source is thin on optimizer practice, replication/HA, MySQL 8 specifics, and performance troubleshooting
- next: add targeted sources or query pages for MySQL indexing, slow query analysis, and replication when needed

## 2026-06-07 08:35 UTC | enrich high-frequency component knowledge
- scope: enrich frequently used component wiki pages and add reusable concept pages
- changed: appended official documentation links, high-frequency usage notes, and extensibility directions to Docker, Kubernetes, Redis, MongoDB, MySQL, and Systemd handbook pages; added concept pages for containerization, Redis topology, MongoDB data modeling, and MySQL isolation; updated `wiki/index.md`
- unresolved: MySQL and systemd official URLs were less reliable from this environment; linked stable official pages instead
- next: add more cross-links between handbook and concept pages, and expand separate ops/debugging pages if usage grows

## 2026-06-07 09:12 UTC | enrich all remaining component pages and add cross-cutting concepts
- scope: complete enrichment of all 17 handbook pages and add 4 new cross-cutting concept pages
- changed: appended official docs, high-frequency usage, and extensibility sections to Etcd, RabbitMQ, NFS, Cgroup, Tmux, Grep, Homebrew, ACME, BIND, Domain Certificate, and VFS handbook pages; added concept pages for DNS resolution, message queue patterns, Linux process management, and container orchestration; updated `wiki/index.md`
- unresolved: some official doc URLs (NFS, cgroup, tmux, grep) were inaccessible from this environment; used alternative official references (man7.org, GitHub wiki, kernel.org)
- next: cross-link handbook pages to concept pages; add more entity pages (e.g., specific tools, services) as usage patterns emerge
