# Cloudinator
Cloudinator dump Cloudflare config in JSON or TERRAFORM

# Cloudinator

Cloudinator is a tool that exports a full **Cloudflare account and zone configuration** and optionally converts it into **Terraform-managed infrastructure**.

It allows you to:

* Dump Cloudflare configuration to JSON
* Merge everything into a single JSON snapshot
* Generate Terraform configuration and state from existing infrastructure
* Safely migrate Cloudflare setups into Infrastructure-as-Code

Typical use cases:

* Cloudflare configuration backup
* Terraform migration
* Security auditing
* Infrastructure documentation

---

# Features

## Account-level export

Cloudinator retrieves:

* Account information
* Account rulesets
* Workers scripts
* Workers KV namespaces
* Zero Trust tunnels
* Access applications
* Access policies
* Load balancer pools
* Load balancer monitors

---

## Zone-level export

For each zone:

* Zone configuration
* DNS records
* Zone settings
* Page rules
* Firewall rules
* Access rules
* User-Agent rules
* Bot management settings
* Workers routes
* Load balancers
* Rulesets

---

## JSON merge mode

Optional **single file export** containing the entire Cloudflare configuration.

```
cloudflare_full_config.json
```

Useful for:

* Backups
* Configuration diffing
* Security audits

---

## Terraform generation

Cloudinator can automatically:

1. Generate Terraform import blocks
2. Import existing Cloudflare resources
3. Generate HCL configuration
4. Build Terraform state

Supported resources:

* DNS records
* Rulesets
* Load balancers
* Load balancer pools
* Load balancer monitors
* Health checks
* Certificate packs
* Cloudflare tunnels
* Page rules
* Worker routes

---

# Requirements

Required tools:

```
bash
curl
jq
terraform (required for terraform mode)
```

Install dependencies on Debian/Ubuntu:

```bash
sudo apt install jq curl
```

Install Terraform:

https://developer.hashicorp.com/terraform/downloads

---

# Installation

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/cloudinator.git
cd cloudinator
chmod +x cloudinator.sh
```

---

# Cloudflare API Token

Create a **Cloudflare API token** with at least the following permissions.

Recommended permissions:

```
Account:
  Workers Scripts: Read
  Workers KV Storage: Read
  Access: Read
  Load Balancers: Read
  Cloudflare Tunnel: Read

Zone:
  DNS: Read
  Firewall: Read
  Rulesets: Read
  Workers Routes: Read
  Page Rules: Read
  Load Balancers: Read
```

Create the token here:

https://dash.cloudflare.com/profile/api-tokens

---

# Usage

```
./cloudinator.sh [command] [options]
```

---

# Commands

## Dump configuration

Export account and zone configuration.

```
./cloudinator.sh dump --token TOKEN --account ACCOUNT_ID
```

Example:

```bash
./cloudinator.sh dump \
  --name MyInfra \
  --token CF_TOKEN \
  --account ACCOUNT_ID
```

Output directory:

```
cf-MyInfra-YYYYMMDD/
```

---

## Dump a single zone

```
./cloudinator.sh dump \
  --token TOKEN \
  --account ACCOUNT_ID \
  --zone ZONE_ID
```

---

## Merge everything into one JSON

```
./cloudinator.sh dump \
  --token TOKEN \
  --account ACCOUNT_ID \
  --merge
```

Result file:

```
cloudflare_full_config.json
```

---

## Generate Terraform configuration

```
./cloudinator.sh terraform \
  --token TOKEN \
  --account ACCOUNT_ID \
  --zone ZONE_ID
```

Cloudinator will:

1. Generate Terraform import blocks
2. Import infrastructure
3. Generate Terraform configuration
4. Build Terraform state

Output directory:

```
terraform/
```

Files generated:

```
main.tf
generated-ZONEID.tf
terraform.tfstate
import.tf
```

---

# Example Workflow

## 1. Dump configuration

```bash
./cloudinator.sh dump \
  --name Production \
  --token CF_TOKEN \
  --account ACCOUNT_ID
```

## 2. Backup entire configuration

```bash
./cloudinator.sh dump \
  --token CF_TOKEN \
  --account ACCOUNT_ID \
  --merge
```

## 3. Convert infrastructure to Terraform

```bash
./cloudinator.sh terraform \
  --token CF_TOKEN \
  --account ACCOUNT_ID \
  --zone ZONE_ID
```

---

# Directory Structure Example

```
cf-Cloudinator-20260327/

account.json
account_rulesets.json
workers_scripts.json
workers_kv.json
tunnels.json

zones/
  ZONE_ID/
    zone.json
    settings.json
    dns_records.json
    page_rules.json
    firewall_rules.json
    access_rules.json
    ua_rules.json
    bot_management.json
    workers_routes.json
    load_balancers.json
    rulesets.json
```

---

# Security Notes

Cloudinator **does not store API tokens** in exported files.

However exported data may contain:

* Infrastructure metadata
* Domain names
* Security policies

Avoid committing exported dumps to public repositories.

---

# License

GPL v2

```
Cloudinator
Copyright (C) Jean-Philippe Guillemin

This program is free software distributed under the GNU General Public License v2.
```

---

# Author

Jean-Philippe Guillemin

---

# Contributions

Pull requests and improvements are welcome.

Possible improvements:

* Support additional Cloudflare APIs
* Terraform module generation
* Improved pagination handling
* Parallel API fetching
* Configuration diff mode
* Restore capability

