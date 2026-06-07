---
title: "Message Queue Patterns Concept"
slug: "concept-message-queue-patterns"
type: concept
created: "2026-06-07T09:07:00Z"
updated: "2026-06-07T09:07:00Z"
sources:
  - "https://www.rabbitmq.com/tutorials"
  - "https://www.rabbitmq.com/docs"
---

# Message Queue Patterns Concept

Message queues decouple producers from consumers. The main design decisions are routing, delivery guarantees, and failure handling.

## Core patterns

| Pattern | Use case | RabbitMQ mechanism |
| --- | --- | --- |
| Work queue | Distribute tasks among workers | Default exchange + competing consumers |
| Pub/Sub | Broadcast to all subscribers | Fanout exchange |
| Routing | Selective delivery by key | Direct exchange with routing keys |
| Topics | Pattern-based routing | Topic exchange with wildcards (`*`, `#`) |
| RPC | Request/reply | Reply-to queue + correlation ID |

## Delivery guarantees

- **At-most-once**: auto-ack — fast but may lose messages.
- **At-least-once**: manual ack + requeue — safe but may duplicate.
- **Exactly-once**: application-level idempotency — queues alone don't provide this.

## Failure handling

- Dead letter exchanges (DLX) route failed/expired messages for inspection.
- TTL on messages or queues bounds how long unprocessed messages live.
- Prefetch count (`basic.qos`) controls how many unacked messages a consumer can hold.

## Useful official references

- RabbitMQ tutorials: [https://www.rabbitmq.com/tutorials](https://www.rabbitmq.com/tutorials)
- RabbitMQ docs: [https://www.rabbitmq.com/docs](https://www.rabbitmq.com/docs)

## Extend this page next

- Add quorum queues vs classic queues comparison.
- Add stream queues for log-style consumption.
- Compare RabbitMQ with Redis Streams and Kafka for different workload shapes.
