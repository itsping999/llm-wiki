---
title: "Acme.sh Handbook"
slug: "handbook-acme"
type: source
created: "2026-06-07T06:23:38Z"
updated: "2026-06-07T09:30:00Z"
sources:
  - "raw/sources/handbooks/Acme/Acme Handbook.md"
  - "https://github.com/acmesh-official/acme.sh/wiki"
---

> Source snapshot: [raw/sources/handbooks/Acme/Acme Handbook.md](<../../raw/sources/handbooks/Acme/Acme Handbook.md>)

# Acme.sh Handbook

acme.sh is a pure Unix shell script implementing the ACME protocol (RFC 8555). It works with Let's Encrypt, ZeroSSL, Google Public CA, Buypass, and other ACME-compatible CAs. Unlike certbot, acme.sh has no Python/systemd dependency — it runs anywhere with `sh`, `curl`, and `openssl`.

## Install

```bash
# method 1: one-liner from web (recommended)
curl https://get.acme.sh | sh -s email=my@example.com

# method 2: git clone and install
git clone --depth 1 https://github.com/acmesh-official/acme.sh.git
cd acme.sh
./acme.sh --install -m my@example.com

# method 3: advanced install with custom paths
./acme.sh --install \
  --home ~/myacme \
  --config-home ~/myacme/data \
  --cert-home ~/mycerts \
  --accountemail "my@example.com" \
  --nocron
```

After install, reload your shell: `source ~/.bashrc`

**Install options:**
- `--home`: install dir (default `~/.acme.sh`)
- `--config-home`: writable dir for certs, keys, configs
- `--cert-home`: custom cert save dir
- `--accountemail`: email for ACME account (receives expiry notices)
- `--nocron`: skip cron job registration (useful for manual/test installs)

## Validation Modes

ACME proves you control a domain via challenge-response. acme.sh supports several modes:

### HTTP-01 (Webroot)

Simplest mode. Places a token file at `http://<domain>/.well-known/acme-challenge/`. Requires a running web server.

```bash
# webroot mode: -w points to the document root
acme.sh --issue -d example.com -d www.example.com -w /var/www/html
```

- Cannot issue wildcard certificates.
- Web server must serve `/.well-known/acme-challenge/` without redirect to HTTPS.

### HTTP-01 (Standalone)

acme.sh starts its own temporary HTTP server on port 80. Use when no web server is running.

```bash
# standalone mode: port 80 must be free
acme.sh --issue -d example.com --standalone
```

- Cannot issue wildcard certificates.
- Port 80 must not be occupied by another service.

### HTTP-01 (Nginx mode)

acme.sh temporarily modifies the Nginx config to serve the challenge. Requires Nginx running locally.

```bash
acme.sh --issue --nginx -d example.com -d www.example.com
```

- Cannot issue wildcard certificates.
- Requires local Nginx installation.

### ALPN-01 (Standalone TLS)

Uses TLS-ALPN-01 challenge on port 443.

```bash
# port 443 must be free
acme.sh --issue --alpn -d example.com
```

### DNS-01 (Manual mode)

Manual TXT record creation. You add `_acme-challenge` TXT records yourself. **Cannot auto-renew.**

```bash
# step 1: issue (acme.sh will tell you what TXT records to add)
acme.sh --issue -d example.com -d "*.example.com" --dns \
  --yes-I-know-dns-manual-mode-enough-go-ahead-please

# step 2: add the TXT records shown in output to your DNS zone

# step 3: renew (must repeat step 2 every time)
acme.sh --renew -d example.com \
  --yes-I-know-dns-manual-mode-enough-go-ahead-please
```

- Supports wildcard certificates.
- **Not recommended for production** — manual intervention required every renewal.
- Consider DNS persist mode or DNS API mode instead.

### DNS-01 (DNS API mode) — Recommended

Automates DNS challenge via your provider's API. Fully supports auto-renewal.

```bash
# set API credentials as env vars, then issue
export Ali_Key="YOUR_ALIYUN_KEY"
export Ali_Secret="YOUR_ALIYUN_SECRET"
acme.sh --issue --dns dns_ali -d example.com -d "*.example.com"
```

## DNS API Providers

acme.sh supports 150+ DNS providers. Set the corresponding env vars before issuing.

| Provider | Hook name | Required env vars |
| --- | --- | --- |
| Aliyun DNS | `dns_ali` | `Ali_Key`, `Ali_Secret` |
| Cloudflare | `dns_cf` | `CF_Token` (API token) or `CF_Key` + `CF_Email` (Global API key) |
| DNSPod (China) | `dns_dp` | `DP_Id`, `DP_Key` |
| DNSPod (International) | `dns_dp_com` | `DP_Id`, `DP_Key` |
| GoDaddy | `dns_gd` | `GD_Key`, `GD_Secret` |
| Amazon Route53 | `dns_aws` | `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` |
| DigitalOcean | `dns_dgon` | `DO_API_KEY` |
| Google Cloud DNS | `dns_gcloud` | `GCE_PROJECT`, service account JSON via `CLOUDSDK_AUTH_CREDENTIAL_FILE_OVERRIDE` |
| Azure DNS | `dns_azure` | `AZUREDNS_SUBSCRIPTIONID`, `AZUREDNS_TENANTID`, `AZUREDNS_APPID`, `AZUREDNS_CLIENTSECRET` |
| Hetzner | `dns_hetzner` | `HETZNER_Token` |
| Vultr | `dns_vultr` | `VULTR_API_KEY` |
| Porkbun | `dns_porkbun` | `PORKBUN_API_KEY`, `PORKBUN_SECRET_API_KEY` |
| TencentCloud (DNSPod) | `dns_tencent` | `Tencent_SecretId`, `Tencent_SecretKey` |
| HuaweiCloud | `dns_huaweicloud` | `HUAWEICLOUD_Username`, `HUAWEICLOUD_Password`, `HUAWEICLOUD_ProjectID` |

Full list: [https://github.com/acmesh-official/acme.sh/wiki/dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

```bash
# example: Cloudflare with API token (recommended)
export CF_Token="your_cf_api_token"
export CF_Zone_ID="your_zone_id"  # optional
acme.sh --issue --dns dns_cf -d example.com -d "*.example.com"

# example: Aliyun DNS
export Ali_Key="your_access_key"
export Ali_Secret="your_access_secret"
acme.sh --issue --dns dns_ali -d example.com -d "*.example.com"

# example: DNSPod (China)
export DP_Id="your_dnspod_id"
export DP_Key="your_dnspod_key"
acme.sh --issue --dns dns_dp -d example.com -d "*.example.com"
```

**DNS Alias Mode**: When your DNS provider has no API, delegate the `_acme-challenge` CNAME to a domain that does. See [https://github.com/acmesh-official/acme.sh/wiki/DNS-alias-mode](https://github.com/acmesh-official/acme.sh/wiki/DNS-alias-mode).

**DNS Persist Mode**: Publish a single long-lived TXT record once, then renewals run automatically forever without per-renewal DNS edits. See [https://github.com/acmesh-official/acme.sh/wiki/DNS-persist-mode](https://github.com/acmesh-official/acme.sh/wiki/DNS-persist-mode).

## Certificate Algorithms

```bash
# ECC certificates (recommended, smaller and faster)
acme.sh --issue -d example.com --keylength ec-256    # ECDSA P-256 (default)
acme.sh --issue -d example.com --keylength ec-384    # ECDSA P-384
acme.sh --issue -d example.com --keylength ec-521    # ECDSA P-521

# RSA certificates
acme.sh --issue -d example.com --keylength 2048
acme.sh --issue -d example.com --keylength 3072
acme.sh --issue -d example.com --keylength 4096

# ECC certs must use --ecc flag for all subsequent operations
acme.sh --renew -d example.com --ecc
acme.sh --install-cert -d example.com --ecc
acme.sh --list --ecc
```

## Deploy Certificates

After issuing, deploy certs to your services. acme.sh supports `--install-cert` (local copy + reload) and `--deploy` (via deploy hooks).

### Nginx

```bash
acme.sh --install-cert -d example.com \
  --key-file       /etc/nginx/ssl/example.com/key.pem \
  --fullchain-file /etc/nginx/ssl/example.com/fullchain.pem \
  --reloadcmd      "systemctl reload nginx"
```

### Apache

```bash
acme.sh --install-cert -d example.com \
  --cert-file      /etc/apache2/ssl/example.com/cert.pem \
  --key-file       /etc/apache2/ssl/example.com/key.pem \
  --fullchain-file /etc/apache2/ssl/example.com/fullchain.pem \
  --reloadcmd      "systemctl reload apache2"
```

### HAProxy

```bash
# HAProxy needs a combined PEM (cert + key)
acme.sh --install-cert -d example.com \
  --fullchain-file /etc/haproxy/ssl/example.com.pem \
  --reloadcmd      "systemctl reload haproxy"
```

### Remote Server via SSH

```bash
export DEPLOY_SSH_USER="deploy"
export DEPLOY_SSH_SERVER="10.0.0.2"
export DEPLOY_SSH_KEYFILE="/etc/ssl/remote/key.pem"
export DEPLOY_SSH_FULLCHAIN="/etc/ssl/remote/fullchain.pem"
export DEPLOY_SSH_REMOTE_CMD="systemctl reload nginx"
acme.sh --deploy -d example.com --deploy-hook ssh
```

### Other Deploy Hooks

acme.sh supports many deploy hooks. Use `acme.sh --deploy -d example.com --deploy-hook <hook>` with the corresponding env vars:

- `cpanel` — cPanel hosting
- `kong` — Kong API gateway
- `gcloud_glb` — Google Cloud Load Balancer
- `f5_bigip` — F5 BIG-IP
- `pan` — Palo Alto Networks
- `haproxy` — HAProxy (via runtime API)
- `synology_dsm` — Synology DSM

Full list: [https://github.com/acmesh-official/acme.sh/wiki/deployhooks](https://github.com/acmesh-official/acme.sh/wiki/deployhooks)

## Certificate Management

```bash
# list all issued certificates
acme.sh --list

# force renew
acme.sh --renew -d example.com --force

# renew with ECC
acme.sh --renew -d example.com --ecc --force

# revoke certificate
acme.sh --revoke -d example.com

# revoke and remove
acme.sh --revoke -d example.com --remove

# remove certificate (stops auto-renewal)
acme.sh --remove -d example.com

# check cron job
acme.sh --cron

# upgrade acme.sh
acme.sh --upgrade

# enable/disable auto-upgrade
acme.sh --upgrade --auto-upgrade
acme.sh --upgrade --auto-upgrade 0
```

## CA Providers

```bash
# switch default CA
acme.sh --set-default-ca --server letsencrypt
acme.sh --set-default-ca --server zerossl
acme.sh --set-default-ca --server buypass
acme.sh --set-default-ca --server google

# issue from a specific CA (per-certificate)
acme.sh --issue -d example.com --server zerossl --standalone
```

| CA | Server name | Notes |
| --- | --- | --- |
| Let's Encrypt | `letsencrypt` | Default. Rate limits: 50 certs/registered domain/week |
| ZeroSSL | `zerossl` | Requires EAB for first use; acme.sh handles it |
| Buypass Go SSL | `buypass` | 180-day certs, free |
| Google Public CA | `google` | Requires Google Cloud project |
| SSL.com | `ssl.com` | EAB required |

## Configuration & Directories

```
~/.acme.sh/                  # acme.sh install home
├── acme.sh                  # main script
├── account.conf             # global config (email, CA, etc.)
├── example.com/             # per-domain directory
│   ├── example.com.key      # private key
│   ├── example.com.cer      # certificate
│   ├── example.com.fullchain.cer  # full chain
│   ├── ca.cer               # CA certificate
│   └── example.com.conf     # per-domain config (env vars, deploy hooks)
└── http.header              # temporary file for HTTP validation
```

Per-domain config (`example.com.conf`) stores:
- `Le_Domain`, `Le_Alt` — domain and SANs
- `Le_Keylength` — key algorithm
- `Le_Webroot` — validation mode
- `Le_DeployHook_*` — deploy hook env vars (persisted across renewals)
- DNS API credentials (persisted after first `--issue`)

## Troubleshooting

```bash
# debug mode
acme.sh --issue -d example.com --standalone --debug

# use staging environment (avoids rate limits during testing)
acme.sh --issue -d example.com --standalone --staging

# force issue (override existing cert)
acme.sh --issue -d example.com --standalone --force

# check if port 80 is available
ss -tlnp | grep :80

# verify certificate after issuance
acme.sh --info -d example.com

# test renewal manually
acme.sh --cron --debug 2
```

Common issues:
- **Rate limited**: use `--staging` for testing; switch to production only when validation works.
- **Challenge fails**: verify DNS propagation (`dig TXT _acme-challenge.example.com`) or web server accessibility (`curl http://example.com/.well-known/acme-challenge/test`).
- **ECC vs RSA mismatch**: always use `--ecc` flag with ECC certs.
- **DNS propagation delay**: wait at least 1 minute after adding TXT records before renewing.

## Official Docs & Extensibility

- Official docs
  - acme.sh wiki: [https://github.com/acmesh-official/acme.sh/wiki](https://github.com/acmesh-official/acme.sh/wiki)
  - acme.sh DNS API: [https://github.com/acmesh-official/acme.sh/wiki/dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)
  - acme.sh deploy hooks: [https://github.com/acmesh-official/acme.sh/wiki/deployhooks](https://github.com/acmesh-official/acme.sh/wiki/deployhooks)
  - Let's Encrypt documentation: [https://letsencrypt.org/docs/](https://letsencrypt.org/docs/)
  - ACME protocol RFC 8555: [https://datatracker.ietf.org/doc/html/rfc8555](https://datatracker.ietf.org/doc/html/rfc8555)

- High-frequency entry points
  - DNS API mode (`--dns dns_xxx`) is the standard for production: auto-renewable, supports wildcards.
  - Always test with `--staging` first, then remove and re-issue without it.
  - Use `--install-cert` with `--reloadcmd` so certs auto-deploy on renewal.

- Extensible directions
  - Add multi-server deployment via SSH hooks or configuration management (Ansible, Salt).
  - Integrate with Kubernetes cert-manager or custom controllers for cloud-native workloads.
  - Add monitoring for certificate expiry (acme.sh doesn't ship a monitoring endpoint).
  - Explore DNS alias mode and DNS persist mode for complex DNS delegation scenarios.
