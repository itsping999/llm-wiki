---
title: "Redis Topology Concept"
slug: "concept-redis-topology"
type: concept
created: "2026-06-07T08:27:00Z"
updated: "2026-06-07T08:27:00Z"
sources:
  - "https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/"
  - "https://redis.io/docs/latest/operate/oss_and_stack/management/cluster/"
  - "https://redis.io/docs/latest/develop/data-types/"
---

# Redis Topology Concept

This page explains how Redis deployments differ by purpose.

## Standalone

Best for local development and simple caches. Easy to reason about, but no automatic failover.

## Sentinel

Sentinel provides high availability by monitoring master and replica instances and promoting a replica when the master fails. It is often enough when the dataset fits on one node but uptime matters.

## Cluster

Cluster shards data across multiple nodes. Use it when dataset size, write throughput, or read fan-out exceeds a single machine.

## Choosing between them

- Use **standalone** when simplicity matters most.
- Use **sentinel** when failover matters but sharding is not needed yet.
- Use **cluster** when data volume or throughput forces horizontal scaling.

## Useful official references

- Sentinel: [https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/)
- Cluster: [https://redis.io/docs/latest/operate/oss_and_stack/management/cluster/](https://redis.io/docs/latest/operate/oss_and_stack/management/cluster/)
- Data types: [https://redis.io/docs/latest/develop/data-types/](https://redis.io/docs/latest/develop/data-types/)

## Extend this page next

- Link this concept to the Redis handbook page.
- Add diagrams for failover and resharding.
- Add notes on client awareness and topology discovery.
