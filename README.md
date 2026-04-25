# FAS Nurseries - Azure Static Web Apps

## Current Editing Note

- Live GitHub Pages output is currently served from [docs/](docs/).
- The [$web/](%24web/) folder is abandoned.
- For practical content updates (address, page copy, contact text), use [docs/EDITING_GUIDE.md](docs/EDITING_GUIDE.md).

This repo hosts the **fasnurseries.com** static website using **Azure Static Web Apps (SWA)**.

## Current Azure Resources (as at April 2026)

| Resource | Type | Resource Group | Action |
|---|---|---|---|
| `fasnurseries.com` | DNS zone | `fasweb` | **Keep** |
| `fasweb` | Storage account | `fasweb_group` | Keep until SWA is live, then retire |
| `fasweb` | Front Door | `fasweb_group` | **Delete** after cutover |
| `faswebcdn` | Front Door | `fasweb_group` | **Delete** after cutover |

> **Why move away from Front Door + Storage?**
> Azure CDN Standard from Microsoft (classic) stopped accepting new profiles and domain onboarding on **August 15, 2025**. Azure Static Web Apps replaces the entire Front Door + Storage pattern with one managed resource — built-in global CDN, free TLS, and CI/CD.

---

## What You Get with Azure Static Web Apps

- Global CDN distribution (built in, no separate CDN resource)
- Free managed TLS/HTTPS for custom domains
- Custom domain support out of the box
- CI/CD via GitHub Actions (auto-generated on creation)
- Staging environments per pull request
- Response header control via `staticwebapp.config.json`
- Free tier available for hobby/small sites

---

## Architecture

```
GitHub repo (this repo)
      │
      │  push to main
      ▼
GitHub Actions (auto-generated workflow)
      │
      ▼
Azure Static Web Apps
  ├── Global CDN edge
  ├── Custom domain + managed HTTPS cert
  └── Serves ./site/** as root
```

---

## Prerequisites

- Azure subscription (resource group `fasweb_group` already exists)
- Azure CLI installed and logged in (`az login`)
- GitHub account with this repo pushed to it
- `fasnurseries.com` DNS zone already in Azure DNS (`fasweb` resource group)

---

## Step 1 — Prepare Your Site Files

Put all static files (HTML, CSS, JS, images) in a folder. The default expected location is:

```
fas_nurseries/
└── site/          <-- your static files go here
    ├── index.html
    ├── 404.html
    └── ...
```

> If you use a build tool (e.g. Vite, Hugo, Eleventy), point `app_location` and `output_location` to your source and build output folders respectively in Step 3.

---

## Step 2 — Create the Static Web App (Azure CLI)

> Resource group `fasweb_group` already exists — no need to recreate it.

```bash
# Variables
RG="fasweb_group"
LOCATION="eastus2"
SWA_NAME="fas-nurseries-swa"
GITHUB_REPO="https://github.com/<your-org>/fas_nurseries"
GITHUB_BRANCH="main"

# Create the Static Web App linked to your GitHub repo
az staticwebapp create \
  --name $SWA_NAME \
  --resource-group $RG \
  --location $LOCATION \
  --source $GITHUB_REPO \
  --branch $GITHUB_BRANCH \
  --app-location "/site" \
  --output-location "" \
  --login-with-github
```

`--login-with-github` opens a browser to authorise Azure to your GitHub repo and automatically creates the GitHub Actions workflow file.

After creation, your site is live at:
```
https://<random-name>.azurestaticapps.net
```

---

## Step 3 — GitHub Actions Workflow

Azure creates `.github/workflows/azure-static-web-apps-<name>.yml` automatically in your repo. It runs on every push to `main` and every pull request.

You can check the generated workflow and adjust `app_location` / `output_location` if needed:

```yaml
# Key section inside the generated workflow
- uses: Azure/static-web-apps-deploy@v1
  with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
    repo_token: ${{ secrets.GITHUB_TOKEN }}
    action: "upload"
    app_location: "/site"       # folder containing your static files
    output_location: ""         # leave blank if no build step
```

---

## Step 4 — Custom Domain + HTTPS

HTTPS is automatically provisioned. Your DNS is already in Azure DNS (`fasnurseries.com` zone in resource group `fasweb`), so use the Azure CLI to add the records — no registrar login needed.

### 4a — Register the hostname with SWA

```bash
# www subdomain
az staticwebapp hostname set \
  --name fas-nurseries-swa \
  --resource-group fasweb_group \
  --hostname "www.fasnurseries.com"

# Apex domain (fasnurseries.com)
az staticwebapp hostname set \
  --name fas-nurseries-swa \
  --resource-group fasweb_group \
  --hostname "fasnurseries.com"
```

### 4b — Add DNS records in Azure DNS

Get your SWA default hostname first:
```bash
az staticwebapp show \
  --name fas-nurseries-swa \
  --resource-group fasweb_group \
  --query defaultHostname -o tsv
# e.g. lively-river-abc123.azurestaticapps.net
```

Then add records to the `fasnurseries.com` zone:

```bash
DNS_RG="fasweb"
ZONE="fasnurseries.com"
SWA_HOST="<output from above e.g. lively-river-abc123.azurestaticapps.net>"

# www CNAME
az network dns record-set cname set-record \
  --resource-group $DNS_RG \
  --zone-name $ZONE \
  --record-set-name www \
  --cname $SWA_HOST

# Apex — Azure DNS supports ALIAS records natively
az network dns record-set a create \
  --resource-group $DNS_RG \
  --zone-name $ZONE \
  --name "@" \
  --target-resource $(az staticwebapp show \
      --name fas-nurseries-swa \
      --resource-group fasweb_group \
      --query id -o tsv)
```

> Azure DNS alias records for the apex domain point directly to the SWA resource — no IP address needed, auto-updates if the SWA IP changes.

Azure will auto-provision and renew the TLS certificate once DNS propagates (usually within 10–30 minutes).

---

## Step 5 — Behaviour Configuration (`staticwebapp.config.json`)

Place a `staticwebapp.config.json` file at the root of your `app_location` to control routing, headers, and error pages:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  },
  "responseOverrides": {
    "404": {
      "rewrite": "/404.html"
    }
  },
  "globalHeaders": {
    "cache-control": "public, max-age=3600",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "SAMEORIGIN"
  }
}
```

---

## Deploying Updates

Just push to `main`. GitHub Actions picks it up automatically.

```bash
git add .
git commit -m "update site content"
git push origin main
```

Pull requests automatically get a **staging URL** for preview before merging.

---

## Useful Commands

```bash
# Check SWA details
az staticwebapp show --name fas-nurseries-swa --resource-group fasweb_group

# List hostnames (including custom domain status)
az staticwebapp hostname list --name fas-nurseries-swa --resource-group fasweb_group

# Get deployment token (for manual/CI use)
az staticwebapp secrets list --name fas-nurseries-swa --resource-group fasweb_group
```

---

## Cutover Plan (Front Door → SWA)

Do this **after** SWA is confirmed working on the `.azurestaticapps.net` URL:

1. Update DNS records in the `fasnurseries.com` zone (Step 4 above).
2. Verify `www.fasnurseries.com` and `fasnurseries.com` resolve to SWA and HTTPS works.
3. Delete the `fasweb` Front Door resource (resource group `fasweb_group`).
4. Delete the `faswebcdn` Front Door resource (resource group `fasweb_group`).
5. Optionally delete or repurpose the `fasweb` storage account once traffic is confirmed on SWA.

---

## Pricing

| Tier | Cost | Suitable for |
|------|------|--------------|
| Free | $0/month | Dev, small sites, hobby |
| Standard | ~$9/month per app | Production, custom auth, more bandwidth |

---

## Notes

- No separate CDN, storage account, or certificate resource to manage.
- If you need WAF, DDoS protection, or multi-origin routing, then Front Door becomes the right tool.
- Azure Static Web Apps supports serverless API routes via Azure Functions — add an `api/` folder if needed later.
