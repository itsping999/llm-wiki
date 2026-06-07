---
title: "DNS Resolution & Authority Concept"
slug: "concept-dns-resolution"
type: concept
created: "2026-06-07T09:05:00Z"
updated: "2026-06-07T09:05:00Z"
sources:
  - "https://bind9.readthedocs.io/en/latest/"
  - "https://letsencrypt.org/docs/how-it-works/"
  - "https://github.com/acmesh-official/acme.sh/wiki"
---

# DNS Resolution & Authority Concept

DNS resolution is easier to troubleshoot when you separate three distinct layers:

1. **Authority** — who owns the zone and its records.
2. **Resolution** — how clients find answers through recursive resolvers.
3. **Validation** — how certificates prove identity for TLS.

## Authoritative DNS (BIND)

The authoritative server is the source of truth for a zone. Zone files define records (A, AAAA, CNAME, MX, NS, SOA). Changes to records must increment the SOA serial and trigger a reload (`rndc reload`). For distributed setups, primary/secondary zone transfers (AXFR/IXFR) propagate changes.

## Recursive resolution

Clients query recursive resolvers (system resolver, public DNS like 8.8.8.8). The resolver walks the delegation chain: root -> TLD -> authoritative. Understanding this chain is key to debugging propagation delays.

## Certificate issuance (ACME)

ACME (Let's Encrypt) proves domain control by placing a challenge token:
- **HTTP-01**: token at `/.well-known/acme-challenge/` — simplest but no wildcards.
- **DNS-01**: TXT record at `_acme-challenge.<domain>` — supports wildcards, requires DNS API access.

The certificate chain (root CA -> intermediate CA -> leaf cert) must be complete for clients to trust it.

## Useful official references

- BIND 9 docs: [https://bind9.readthedocs.io/en/latest/](https://bind9.readthedocs.io/en/latest/)
- Let's Encrypt how it works: [https://letsencrypt.org/docs/how-it-works/](https://letsencrypt.org/docs/how-it-works/)
- acme.sh wiki: [https://github.com/acmesh-official/acme.sh/wiki](https://github.com/acmesh-official/acme.sh/wiki)

## Extend this page next

- Add split-horizon DNS patterns (internal vs external resolution).
- Add DNSSEC chain of trust diagram.
- Add certificate monitoring and rotation automation.
