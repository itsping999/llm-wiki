---
title: "Linux Process Management Concept"
slug: "concept-linux-process-management"
type: concept
created: "2026-06-07T09:09:00Z"
updated: "2026-06-07T09:09:00Z"
sources:
  - "https://man7.org/linux/man-pages/man7/cgroups.7.html"
  - "https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html"
  - "https://systemd.io/"
---

# Linux Process Management Concept

On modern Linux, process management has three layers:

1. **Init/service supervision** — systemd manages lifecycle (start, stop, restart).
2. **Resource control** — cgroups limit CPU, memory, I/O per group.
3. **Observability** — tools like `ps`, `top`, `systemd-cgtop`, and `journalctl` show what's running.

## systemd as supervisor

systemd units define how processes start (`ExecStart`), when they restart (`Restart=`), and what they depend on (`After=`, `Requires=`). The service type (simple, forking, oneshot, notify) tells systemd how to track the main process.

## cgroups for resource control

cgroup v2 provides a unified hierarchy. Key controllers:
- **cpu**: `cpu.weight` (proportional share), `cpu.max` (hard cap).
- **memory**: `memory.max` (hard limit), `memory.high` (pressure trigger).
- **io**: `io.max` (I/O bandwidth limit).

On systemd systems, `systemctl set-property` or unit file directives (`CPUWeight=`, `MemoryMax=`) configure cgroups without manual filesystem manipulation.

## Container runtime integration

Docker and containerd create cgroups for each container. Kubernetes uses cgroups for pod-level resource limits. Understanding the cgroup tree helps debug container resource issues (`systemd-cgls`, `systemd-cgtop`).

## Useful official references

- cgroups(7): [https://man7.org/linux/man-pages/man7/cgroups.7.html](https://man7.org/linux/man-pages/man7/cgroups.7.html)
- cgroup v2: [https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html)
- systemd: [https://systemd.io/](https://systemd.io/)

## Extend this page next

- Add OOM killer behavior and tuning.
- Add PSI (Pressure Stall Information) monitoring.
- Add namespace isolation (pid, net, mnt, uts) as a complement to cgroups.
