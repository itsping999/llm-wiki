---
title: "MySQL Knowledge Map"
slug: "query-mysql-knowledge-map"
type: query
created: "2026-06-07T06:37:32Z"
updated: "2026-06-07T06:37:32Z"
sources:
  - "raw/sources/handbooks/MySQL/MySQL Handbook.md"
---

# MySQL Knowledge Map

Question: MySQL 的知识有哪些？

Current source coverage comes from [[handbook-mysql]]. It is strongest on SQL categories, InnoDB storage/transaction internals, locks, logs, and backup/recovery. It is weaker on query optimization, index design practice, replication topology, high availability, performance troubleshooting, and version-specific MySQL 8 features.

## SQL Language Categories

- DQL: querying records with `select`.
- DDL: defining and changing database objects with `create`, `drop`, `alter`, and `truncate`; DDL is treated as implicit commit and cannot be rolled back in the handbook.
- DML: changing table records with `insert`, `delete`, and `update`; DML can be controlled by explicit transaction commit/rollback.
- DCL: permission control with `grant` and `revoke`.
- TCL: transaction control with `rollback`, `savepoint`, `commit`, and `set transaction`.

## InnoDB Storage Model

- Database files: `.opt` for database charset/collation metadata, `.frm` for older table structure metadata, and `.ibd` for file-per-table InnoDB table data/index storage.
- Logical storage hierarchy: tablespace -> segment -> extent -> page -> row.
- Page is the basic InnoDB disk/memory I/O unit, typically 16 KB.
- Extent is usually 1 MB and groups 64 pages to reduce random I/O.
- Segments include data segments, index segments, and rollback segments.

## Row Format And Record Layout

- InnoDB row formats include Redundant, Compact, Dynamic, and Compressed.
- Compact row format contains extra record metadata and real column data.
- Important hidden fields: `row_id`, `trx_id`, and `roll_pointer`.
- `varchar` maximum practical length depends on the 65,535 byte row limit, charset bytes per character, nullable bitmap, and variable-length metadata.
- Row overflow stores large column payload outside the main page and keeps a pointer in the record.

## Transactions

- Transaction guarantees are ACID: atomicity, consistency, isolation, durability.
- InnoDB uses redo log for durability, undo log for atomic rollback, and MVCC/locks for isolation.
- Parallel transaction anomalies covered: dirty read, non-repeatable read, and phantom read.
- Isolation levels covered: read uncommitted, read committed, repeatable read, serializable.
- InnoDB default isolation is repeatable read.
- Read committed and repeatable read use Read View; read committed refreshes Read View per statement, repeatable read keeps one Read View for the transaction.

## Locks

- Global lock: makes the whole database read-only, commonly used for logical full backups.
- Table-level locks: table locks, metadata locks, intention locks, and auto-increment locks.
- Row-level locks: shared lock, exclusive lock, record lock, gap lock, next-key lock, and insert intention lock.
- Gap locks and next-key locks are tied to preventing phantom reads under repeatable read.

## Operational Commands

- Inspect connections: `show processlist`.
- Kill a connection by id: `kill connection <id>`.
- Inspect connection limits: `show variables like 'max_connections'` and `show status like 'Threads_connected'`.
- Inspect data directory: `show variables like 'datadir'`.
- Use global/table locks: `flush tables with read lock`, `unlock tables`, `lock tables <table> read/write`.

## Logs

- Redo log: physical page change log; supports crash recovery and transaction durability.
- Undo log: logical inverse operations; supports rollback and MVCC.
- Binlog: logical SQL/change log; used for backup and replication. `sync_binlog` controls binlog flush frequency.

## Backup And Recovery

- Backup types covered: physical backup, logical backup, filesystem snapshot backup, binlog incremental backup, and replica-side backup.
- `mysqldump` flow includes FTWRL, snapshot read for InnoDB, dumping non-InnoDB tables, releasing global read lock, then dumping InnoDB tables.
- Binlog recovery uses `mysqlbinlog` with time or position ranges, then pipes or exports SQL for replay.

## Gaps To Fill Later

- Index design and optimizer behavior.
- `EXPLAIN`, slow query log, performance schema, and query tuning.
- Replication setup details, GTID, semi-sync, delayed replica, and failover tooling.
- MySQL 8 data dictionary changes, roles, window functions, CTEs, histograms, and invisible indexes.
- Backup tooling beyond `mysqldump`, such as XtraBackup or MySQL Enterprise Backup.
