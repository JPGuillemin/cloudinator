# Cloudinator

**Cloudinator** is a CLI tool that exports your full Cloudflare configuration to JSON and/or Terraform — useful for backups, audits, and Infrastructure-as-Code migrations.

---

## Features

| Feature | Description |
|---|---|
| 📦 JSON dump | Export account & zone config to structured JSON files |
| 🔀 Merge mode | Combine all exports into a single `cloudflare_full_config.json` |
| 🏗️ Terraform generation | Auto-generate HCL config and import blocks from live infrastructure |
| 🔌 Plugin system | Extensible resource plugins under `./plugins/` |

---

## Requirements

- `bash`
- `curl`
- `jq`
- `terraform` *(Terraform mode only)*

Install on Debian/Ubuntu:
```bash
sudo apt install jq curl
```

Install Terraform: https://developer.hashicorp.com/terraform/downloads

---

## Installation
```bash
git clone https://github.com/YOUR_USERNAME/cloudinator.git
cd cloudinator
chmod +x cloudinator
```

---

## Cloudflare API Token

Create a token at https://dash.cloudflare.com/profile/api-tokens with the following permissions:

**Account level:**
- Workers Scripts: Read
- Workers KV Storage: Read
- Access: Read
- Load Balancers: Read
- Cloudflare Tunnel: Read

**Zone level:**
- DNS: Read
- Firewall: Read
- Rulesets: Read
- Workers Routes: Read
- Page Rules: Read
- Load Balancers: Read

---

## Usage
```
./cloudinator [COMMAND] [OPTIONS]
```

### Commands

| Command | Description |
|---|---|
| `dump` | Export account and zone configuration to JSON |
| `terraform` | Generate Terraform configuration from existing infrastructure |
| `resources` | List available resource plugins |

### Options

| Option | Description |
|---|---|
| `--name <name>` | Label used in the output directory name (default: `Cloudinator`) |
| `--token <token>` | Cloudflare API token **(required)** |
| `--account <id>` | Cloudflare account ID **(required for dump)** |
| `--zone <id>` | Restrict to a specific zone (required for `terraform`) |
| `--merge` | Combine all JSON output into one file |
| `--help` | Show help |

---

## Examples

### Dump full account configuration
```bash
./cloudinator dump \
  --name Production \
  --token CF_TOKEN \
  --account ACCOUNT_ID
```

### Dump a single zone
```bash
./cloudinator dump \
  --token CF_TOKEN \
  --account ACCOUNT_ID \
  --zone ZONE_ID
```

### Merge everything into one JSON snapshot
```bash
./cloudinator dump \
  --token CF_TOKEN \
  --account ACCOUNT_ID \
  --merge
```

### Generate Terraform configuration
```bash
./cloudinator terraform \
  --token CF_TOKEN \
  --account ACCOUNT_ID \
  --zone ZONE_ID
```

---

## Output Structure

### JSON dump (`cf-<name>-<date>/`)
```
cf-Production-20260330/
├── account.json
├── account_rulesets.json
├── workers_scripts.json
├── workers_kv.json
├── tunnels.json
├── tunnels/
│   ├── <tunnel-id>.json
│   └── <tunnel-id>-config.json
├── access_apps.json
├── access_policies.json
├── lb_monitors.json
├── lb_pools.json
├── zones.json
├── zones/
│   └── <zone-id>/
│       ├── zone.json
│       ├── settings.json
│       ├── dns_records.json
│       ├── page_rules.json
│       ├── firewall_rules.json
│       ├── access_rules.json
│       ├── ua_rules.json
│       ├── bot_management.json
│       ├── workers_routes.json
│       ├── load_balancers.json
│       └── rulesets.json
└── cloudflare_full_config.json   ← only with --merge
```

### Terraform output (`terraform/`)
```
terraform/
├── main.tf
├── import.tf
├── plan.bin
├── cache-rules-<zone-id>.tf
├── configuration-rules-<zone-id>.tf
├── dns-records-<zone-id>.tf
├── healthchecks-<zone-id>.tf
├── load-balancers-<zone-id>.tf
├── load-balancers-monitors-<account-id>.tf
├── load-balancers-pools-<account-id>.tf
├── rate-limiting-rules-<zone-id>.tf
├── response-header-rules-<zone-id>.tf
├── tunnels-<account-id>.tf
├── waf-custom-rules-<zone-id>.tf
└── waf-managed-overrides-<zone-id>.tf
```

---

## Plugin System

Resource plugins live in `./plugins/`. Each plugin is a bash script responsible for generating Terraform import blocks and HCL configuration for a specific Cloudflare resource type.

List available plugins:
```bash
./cloudinator resources
```

Current plugins:

| Plugin | Resource |
|---|---|
| `CacheRules` | Cache Rules |
| `CompressionRules` | Compression Rules |
| `ConfigurationRules` | Configuration Rules |
| `Healthchecks` | Health Checks |
| `LbMonitors` | Load Balancer Monitors |
| `LbPools` | Load Balancer Pools |
| `LoadBalancers` | Load Balancers |
| `OriginRules` | Origin Rules |
| `PageRules` | Page Rules |
| `RateLimitingRules` | Rate Limiting Rules |
| `Records` | DNS Records |
| `RedirectRules` | Redirect Rules |
| `ResponseHeaderRules` | Response Header Rules |
| `RewriteRules` | Rewrite Rules |
| `Tunnels` | Cloudflare Tunnels |
| `WafCustomRules` | WAF Custom Rules |
| `WafManagedRulesetOverrides` | WAF Managed Ruleset Overrides |

---

## Security Notes

Cloudinator does **not** embed API tokens in any exported file. That said, exported data may contain sensitive infrastructure details (domain names, security policies, routing config). **Do not commit exports to public repositories.**

---

## License

GPL v2 — Copyright © Jean-Philippe Guillemin

---

## Author

Jean-Philippe Guillemin — hyp3ri0n@sfr.fr

---

## Contributing

Pull requests welcome. Areas for improvement:

- Additional Cloudflare API coverage
- Terraform module generation
- Pagination handling for large accounts
- Parallel API fetching
- Configuration diff / drift detection mode
- Restore / apply capability