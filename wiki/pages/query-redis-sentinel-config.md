---
title: "Redis Sentinel Configuration"
slug: "query-redis-sentinel-config"
type: query
created: "2026-06-07T06:29:44Z"
updated: "2026-06-07T06:29:44Z"
sources:
  - "raw/sources/handbooks/Redis/Redis Handbook.md"
---

# Redis Sentinel Configuration

Question: Redis 哨兵怎么配置？

Use Sentinel for non-clustered Redis high availability: one master, one or more replicas, and at least three Sentinel processes on independently failing nodes. With three Sentinels, a common quorum is `2`; quorum decides when a master is considered objectively down, while actual failover still needs Sentinel majority authorization.

## Redis Data Nodes

Configure every Redis data node with the same authentication settings, because any node may become a master after failover.

```conf
bind 10.0.0.11
port 6379
daemonize yes
dir /data/redis/6379
logfile /var/log/redis/redis-6379.log
dbfilename dump-6379.rdb

requirepass <redis-password>
masterauth <redis-password>
```

On each replica, point it at the current master. For Redis 5+, prefer `replicaof`; older material may use `slaveof`.

```conf
replicaof 10.0.0.10 6379
```

Runtime equivalent:

```bash
redis-cli -h <replica-ip> -p 6379 -a '<redis-password>' REPLICAOF <master-ip> 6379
```

Start each data node:

```bash
redis-server /path/to/redis.conf
```

## Sentinel Nodes

Create a writable `sentinel.conf` on each Sentinel node. Sentinel rewrites this file after discovery and failover, so do not mount it read-only.

```conf
bind 10.0.0.21
port 26379
daemonize yes
dir /data/redis-sentinel/26379
logfile /var/log/redis/sentinel-26379.log

sentinel monitor mymaster 10.0.0.10 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
sentinel auth-pass mymaster <redis-password>
```

If Redis data nodes use Redis 6+ ACL credentials, add the Sentinel user as well:

```conf
sentinel auth-user mymaster <redis-acl-user>
sentinel auth-pass mymaster <redis-acl-password>
```

If clients must authenticate to Sentinel itself, use the same password on all Sentinel instances:

```conf
requirepass "<sentinel-password>"
```

Start Sentinel with either form:

```bash
redis-sentinel /path/to/sentinel.conf
redis-server /path/to/sentinel.conf --sentinel
```

## Verify

```bash
redis-cli -h <sentinel-ip> -p 26379 SENTINEL master mymaster
redis-cli -h <sentinel-ip> -p 26379 SENTINEL replicas mymaster
redis-cli -h <sentinel-ip> -p 26379 SENTINEL sentinels mymaster
redis-cli -h <sentinel-ip> -p 26379 SENTINEL get-master-addr-by-name mymaster
```

If Sentinel itself has `requirepass`, add `-a '<sentinel-password>'`.

Manual failover test:

```bash
redis-cli -h <sentinel-ip> -p 26379 SENTINEL failover mymaster
```

After failover, verify that:

- `SENTINEL get-master-addr-by-name mymaster` returns the new master.
- `INFO replication` on Redis nodes shows one master and replicas following it.
- Application clients use Sentinel discovery, not a hard-coded Redis master address.

## Notes

- `parallel-syncs 1` is conservative: only one replica is reconfigured at a time after failover.
- In Docker, NAT, or port-remapped environments, Sentinel auto-discovery can break unless announced addresses are configured correctly.
- Keep `requirepass` and `masterauth` aligned on all Redis data nodes unless there is a deliberate replica-priority design.
