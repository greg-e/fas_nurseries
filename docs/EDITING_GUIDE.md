# Editing Guide — Frank A. Smith Nurseries

The live site is served by GitHub Pages from the `docs/` folder of this repository.
Push any changes to `main` and the site updates automatically within a minute or two.

---

## Repository Tree (Current)

This is the current practical structure of the repo and what each path is for.

```text
fas_nurseries/
├── README.md
└── docs/
	├── .nojekyll
	├── CNAME
	├── EDITING_GUIDE.md
	├── favicon-32x32.png
	├── styles.6d2842a8ac114ab1800e.css
	├── index.html
	├── 404.html
	├── 404/
	│   └── index.html
	├── growing/
	│   └── index.html
	├── register/
	│   └── index.html
	├── sent/
	│   └── index.html
	├── icons/
	│   ├── icon-48x48.png
	│   ├── icon-72x72.png
	│   ├── icon-96x96.png
	│   ├── icon-144x144.png
	│   ├── icon-192x192.png
	│   ├── icon-256x256.png
	│   ├── icon-384x384.png
	│   └── icon-512x512.png
	└── static/
		├── site images (banner, team photos, plant photos)
		└── font files (fa-brands, fa-regular, fa-solid)
```

### Path-by-Path Details

| Path | Purpose | Edit Guidance |
|---|---|---|
| `README.md` | Project-level deployment and infrastructure notes | Edit only for platform/process documentation updates |
| `docs/` | Live website root for GitHub Pages | All content changes happen here |
| `docs/.nojekyll` | Tells GitHub Pages not to run Jekyll processing | Keep; do not edit |
| `docs/CNAME` | Custom domain mapping (`fasnurseries.com`) | Keep exact value unless domain changes |
| `docs/EDITING_GUIDE.md` | Human editing instructions (this file) | Keep current with actual repo structure and workflow |
| `docs/favicon-32x32.png` | Browser tab/site icon | Replace only if branding changes |
| `docs/styles.6d2842a8ac114ab1800e.css` | Main site stylesheet and font references | Edit carefully; affects all pages |
| `docs/index.html` | Home page | Frequent content updates |
| `docs/growing/index.html` | Plant availability page | Most frequent updates |
| `docs/register/index.html` | Signup page | Usually rare edits |
| `docs/sent/index.html` | Signup confirmation page | Rare edits |
| `docs/404.html` | Not found page | Keep in sync with `docs/404/index.html` |
| `docs/404/index.html` | Routing-compatible duplicate 404 page | Keep in sync with `docs/404.html` |
| `docs/icons/` | Apple touch icons for multiple device sizes | Keep all sizes; referenced by page head tags |
| `docs/static/` | Images and Font Awesome assets used by HTML/CSS | Safe to add files; remove only after confirming no references |

---

## Files You Edit

| File | What it is |
|---|---|
| `docs/index.html` | Home page (banner, history, team, contact form) |
| `docs/growing/index.html` | Plant availability tables |
| `docs/register/index.html` | Standalone email sign-up page |
| `docs/sent/index.html` | Confirmation page shown after sign-up |
| `docs/404.html` | 404 error page |
| `docs/404/index.html` | Duplicate 404 for routing compatibility — keep in sync with `docs/404.html` |

Everything else in `docs/` should be treated as shared assets/config unless you explicitly confirm it is unused.

---

## Contact Details — Shared Across All Pages

These items are **hardcoded in every HTML file individually** — there is no shared template. If any of the following changes, you must update it in all 6 files listed above.

| Detail | Current value |
|---|---|
| Address | 1310 Austell Road SE, Marietta, GA 30008 |
| Main phone | (770) 454-7994 |
| JR's cell | (770) 352-4407 |
| Darren's cell | (770) 530-5078 |
| JR's email | jr@fasnurseries.com |
| Darren's email (footer link) | darren@fasnurseries.com |
| Facebook | https://www.facebook.com/Frank-A-Smith-Nurseries-Inc-237835649948718 |

The footer (address, phone, Facebook icon, email icon) is repeated on every page. The "Get in touch" section with both cells and emails only appears on `docs/index.html`.

---

## Email Sign-Up Infrastructure

### Azure Logic App Workflow

The sign-up forms on `docs/index.html` and `docs/register/index.html` both POST to the same Azure Logic Apps URL:

```
https://prod-28.centralus.logic.azure.com:443/workflows/ce29f378395245b08090935fa449a6c9/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=bzJehT9XgM_dLsB_yJTaocQo_kCATKbwtOH1Y-SgSlQ
```

**To manage this workflow:**

1. Log in to [Azure Portal](https://portal.azure.com)
2. Navigate to **Resource Groups** → `fasweb_group`
3. Find the **Logic App** resource (search for "logic" or look for the workflow ID `ce29f378395245b08090935fa449a6c9`)
4. Click to open the workflow designer
5. Check the trigger and actions to see where emails are being sent

**If you need to change:**
- **Where signup emails go** — edit the Logic App's email action (usually an "Send an email" step)
- **Email content/template** — edit the email body in the Logic App
- **Webhook signature** — if the signature in the URL changes, update both HTML files

**If the Logic App URL changes:**
Update the `action="..."` attribute in both:
- `docs/index.html` (in the `#contact-form` form element)
- `docs/register/index.html` (in the registration form)

---

## Domain & DNS Configuration

### Current Domain

**Domain:** `fasnurseries.com`  
**Registrant:** (Check your domain registrar for domain registration details)  
**Where it's configured:** `docs/CNAME` (GitHub Pages custom domain config)

### DNS Zone Management

**Location:** Azure DNS (resource group `fasweb_group`)  
**Zone name:** `fasnurseries.com`

**Current DNS records** (as of April 2026):

| Record | Type | Target | Purpose |
|--------|------|--------|---------|
| @ (root) | A (or ALIAS) | GitHub Pages IP | Root domain resolution |
| www | CNAME | GitHub Pages hostname | www subdomain |

**To manage DNS:**

1. Log in to [Azure Portal](https://portal.azure.com)
2. Navigate to **Resource Groups** → `fasweb_group`
3. Find the **DNS Zone** named `fasnurseries.com`
4. Click to open the zone
5. View/edit records as needed

**Why Azure DNS?**
- Domain name system (DNS) is separate from web hosting
- This allows you to keep the domain pointing to GitHub Pages while keeping DNS records in Azure
- If you ever need to point the domain elsewhere (new host, subdomain for email, etc.), update DNS records here

### CNAME File

The file `docs/CNAME` tells GitHub Pages which custom domain to use:

```
fasnurseries.com
```

Keep this file exactly as is. GitHub Pages reads it automatically.

---

## Plant Availability Page (`docs/growing/index.html`)

This is the most frequently updated page. It contains four sections:

1. **Perennials and Groundcover** (`#one`)
2. **Hosta** (`#two`)
3. **Ferns** (`#three`)
4. **Grasses** (`#four`)

Each section has a table with three columns: **Botanical Name | Common Name | Size**.

To add or remove a plant, find the right `<table>` section and add/remove a `<tr>` row. Size values currently in use: `4"`, `1 gal`, `2 gal`, `3 gal`.

Each section also has a featured plant image at the top. Plant images are stored in `docs/static/` with hashed filenames (e.g., `chocolatechip-7551a925925ad379ee498d1d4c71b469.jpg`). If you replace an image, add the new file to `docs/static/` and update the `src` attribute in the HTML.

---

## Home Page Sections (`docs/index.html`)

| Section | What to look for when editing |
|---|---|
| Banner | "Serving the Atlanta area since 1940" tagline; logo (`whitelogo-*.png`) |
| History ("We're well rooted") | Three paragraphs covering 1930s–2006 company history |
| Team | JR and Darren — names, phones, emails, and headshot images |
| Get in touch | Phone numbers and email addresses for both team members |
| Join our email list | Form subtext: "We routinely send our current availability and pricing" |

---

## Images

All site images live in `docs/static/`. Filenames include a content hash (e.g., `banner-bb904a5dbb14a81014f22e438d21f417.jpg`). When replacing an image:

1. Add the new image file to `docs/static/` (keep the same filename if content hasn't changed, or use a new descriptive name).
2. Update the `src` or `url(...)` reference in the HTML file.
3. The old image file can be deleted, but leaving it causes no harm.

Current images:

This is the most frequently updated page. It contains four sections:

1. **Perennials and Groundcover** (`#one`)
2. **Hosta** (`#two`)
3. **Ferns** (`#three`)
4. **Grasses** (`#four`)

Each section has a table with three columns: **Botanical Name | Common Name | Size**.

To add or remove a plant, find the right `<table>` section and add/remove a `<tr>` row. Size values currently in use: `4"`, `1 gal`, `2 gal`, `3 gal`.

Each section also has a featured plant image at the top. Plant images are stored in `docs/static/` with hashed filenames (e.g., `chocolatechip-7551a925925ad379ee498d1d4c71b469.jpg`). If you replace an image, add the new file to `docs/static/` and update the `src` attribute in the HTML.

---

## Home Page Sections (`docs/index.html`)

| Section | What to look for when editing |
|---|---|
| Banner | "Serving the Atlanta area since 1940" tagline; logo (`whitelogo-*.png`) |
| History ("We're well rooted") | Three paragraphs covering 1930s–2006 company history |
| Team | JR and Darren — names, phones, emails, and headshot images |
| Get in touch | Phone numbers and email addresses for both team members |
| Join our email list | Form subtext: "We routinely send our current availability and pricing" |

---

## Images

All site images live in `docs/static/`. Filenames include a content hash (e.g., `banner-bb904a5dbb14a81014f22e438d21f417.jpg`). When replacing an image:

1. Add the new image file to `docs/static/` (keep the same filename if content hasn't changed, or use a new descriptive name).
2. Update the `src` or `url(...)` reference in the HTML file.
3. The old image file can be deleted, but leaving it causes no harm.

Current images:

| File | Used for |
|---|---|
| `banner-bb904a5dbb14a81014f22e438d21f417.jpg` | Home page hero banner background |
| `whitelogo-7599bb0cb9f735c6810bb6cada3bc023.png` | Site logo (top of banner) |
| `jr-11fd30fdef8032e58aa5c31b44b26082.jpg` | JR headshot |
| `darren-emerick-d385a09555f529938e8ffb0072fdce61.jpg` | Darren headshot |
| `chocolatechip-*.jpg`, `asiaticjasmine-*.jpg`, etc. | Plant feature photos on growing page |

---

## Safe Editing Workflow

1. Edit the file(s) in `docs/` directly.
2. Preview locally by opening the HTML file in a browser, or push and check the live site.
3. Commit with a short message describing the change.
4. Push to `main` — GitHub Pages deploys automatically.

**Tip:** These HTML files are minified to a single line. Use your editor's Find & Replace (Ctrl+H) rather than trying to read the raw HTML. Search for the exact text you want to change.

---

## Menu Behavior Note

The navigation menu uses inline JavaScript injected into each page (not a framework). On page load, `#menu` is moved to `document.body` and `is-menu-visible` is cleared. If the menu ever stops responding to clicks, the most likely cause is a z-index or overlay conflict — the `#page-wrapper` element can intercept clicks if the menu is not moved to `body`.
