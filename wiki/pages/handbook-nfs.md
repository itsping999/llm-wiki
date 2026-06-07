---
title: "NFS Handbook"
slug: "handbook-nfs"
type: source
created: "2026-06-07T06:23:38Z"
updated: "2026-06-07T06:23:38Z"
sources:
  - "raw/sources/handbooks/NFS/NFS Handbook.md"
---

> Source snapshot: [raw/sources/handbooks/NFS/NFS Handbook.md](<../../raw/sources/handbooks/NFS/NFS Handbook.md>)

# NFS Handbook

# NFS Server

```bash
# ubuntu
# 安装 nfs-kernel-server
sudo apt insatll nfs-kernel-server
# 创建一个共享目录
sudo mkdir /nfs
sudo chown nobody:nogroup /nfs
sudo chmod 777 /nfs
# 设置共享目录配置文件
sudo vi /etc/exports
/nfs *(rw,sync,no_subtree_check)
# 重新加载 nfs
sudo exportfs -r
sudo systemctl restart nfs-kernel-server

# sudo systemctl stop nfs-kernel-server
# sudo systemctl stop rpcbind
# sudo systemctl stop rpcbind.socket

# centos
yum install -y nfs-utils rpcbind
chown nfsnobody:nfsnobody /nfs
sudo chmod 777 /nfs

vi /etc/exports

/nfs_webscada_data *(rw,sync,no_subtree_check)
/nfs_webscada_static *(rw,sync,no_subtree_check)

exportfs -r
```

# NFS Client

```bash
# 安装 nfs-common
sudo apt install nfs-common
# 挂载 nfs 共享目录到本地
sudo mount.ntfs {{NFS_SERVER_IPADDR}}:{{FROM_DIR}} {{TO_DIR}}
```

## Official Docs & Extensibility

- Official docs
  - NFS Wiki: [https://linux-nfs.org/wiki/index.php/Main_Page](https://linux-nfs.org/wiki/index.php/Main_Page)
  - NFS man page: [https://man7.org/linux/man-pages/man5/exports.5.html](https://man7.org/linux/man-pages/man5/exports.5.html)
  - NFS client mount options: [https://man7.org/linux/man-pages/man8/mount.nfs.8.html](https://man7.org/linux/man-pages/man8/mount.nfs.8.html)

- High-frequency entry points
  - `/etc/exports` and `exportfs -ra` are the server-side daily commands; `mount -t nfs` and `/etc/fstab` are the client side.
  - NFSv4 is the recommended version for modern deployments; NFSv3 is still common in legacy setups.
  - Always check `showmount -e <server>` before debugging mount failures.

- Extensible directions
  - Add NFSv4.1/pNFS for parallel I/O and better scalability.
  - Cover Kerberos-based NFS security (`sec=krb5`) for production environments.
  - Add troubleshooting for stale file handles, permission issues, and network partition behavior.
