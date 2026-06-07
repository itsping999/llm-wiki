---
title: "BIND Handbook"
slug: "handbook-bind"
type: source
created: "2026-06-07T06:23:38Z"
updated: "2026-06-07T06:23:38Z"
sources:
  - "raw/sources/handbooks/BIND/BIND Handbook.md"
---

> Source snapshot: [raw/sources/handbooks/BIND/BIND Handbook.md](<../../raw/sources/handbooks/BIND/BIND Handbook.md>)

# BIND Handbook

# Install

```bash
mkdir -p /root/docker/bind

docker run --name bind -d --restart=always --publish 53:53/tcp --publish 53:53/udp --publish 10000:10000/tcp --volume /root/docker/bind:/data sameersbn/bind:9.16.1-20200524

# enter the container
docker exec -it bind bash

# set ssl mode to 0
sed -i '10s/.*/ssl=0/g' etc/webmin/miniserv.conf

# exit container exit
cd /root/docker/bind/bind/etc
cat >> resolv.conf << EOF
nameserver 114.114.114.114
nameserver 8.8.8.8
EOF

chmod 775 resolv.conf
chown 101.101 resolv.conf

vi named.conf.options
# add a line
allow-query { any; };

# restart dns server
docker restart bind
```

# Configuration

> Username: root Password: password
>

![./assets/NotesDNS_Server_HandbookUntitled.png](../../raw/sources/handbooks/BIND/assets/NotesDNS_Server_HandbookUntitled.png)

![./assets/NotesDNS_Server_HandbookUntitled_1.png](../../raw/sources/handbooks/BIND/assets/NotesDNS_Server_HandbookUntitled_1.png)

![./assets/NotesDNS_Server_HandbookUntitled_2.png](../../raw/sources/handbooks/BIND/assets/NotesDNS_Server_HandbookUntitled_2.png)

![./assets/NotesDNS_Server_HandbookUntitled_3.png](../../raw/sources/handbooks/BIND/assets/NotesDNS_Server_HandbookUntitled_3.png)

![./assets/NotesDNS_Server_HandbookUntitled_4.png](../../raw/sources/handbooks/BIND/assets/NotesDNS_Server_HandbookUntitled_4.png)

![./assets/NotesDNS_Server_HandbookUntitled_5.png](../../raw/sources/handbooks/BIND/assets/NotesDNS_Server_HandbookUntitled_5.png)

## Official Docs & Extensibility

- Official docs
  - BIND 9 documentation: [https://bind9.readthedocs.io/en/latest/](https://bind9.readthedocs.io/en/latest/)
  - BIND 9 configuration reference: [https://bind9.readthedocs.io/en/latest/chapter3.html](https://bind9.readthedocs.io/en/latest/chapter3.html)
  - BIND 9 ARM (Administrator Reference Manual): [https://bind9.readthedocs.io/en/latest/arm.html](https://bind9.readthedocs.io/en/latest/arm.html)

- High-frequency entry points
  - `named-checkconf` and `named-checkzone` should always be used before reloading BIND.
  - Zone file syntax (SOA, NS, A, CNAME, MX) is the core knowledge; get comfortable with serial number conventions.
  - `rndc reload` and `rndc flush` are the daily management commands.

- Extensible directions
  - Add DNSSEC signing and key management for production DNS.
  - Cover views for split-horizon DNS (internal vs external resolution).
  - Add TSIG-based zone transfers and dynamic DNS update patterns.
