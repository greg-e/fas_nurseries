# Editing Guide — Frank A. Smith Nurseries

The live site is served by GitHub Pages from the `docs/` folder of this repository.
Push any changes to `main` and the site updates automatically within a minute or two.

---

## Files You Edit

| File | What it is |
|---|---|
| `docs/index.html` | Home page (banner, history, team, contact form) |
| `docs/growing/index.html` | Plant availability tables |
| `docs/register/index.html` | Standalone email sign-up page |
| `docs/sent/index.html` | Confirmation page shown after sign-up |
| `docs/404.html` | 404 error page |
| `docs/404/index.html` | Duplicate 404 (Gatsby routing artifact — keep in sync with 404.html) |

Everything else in `docs/` (hashed `.js` files, `.map` files, `chunk-map.json`, `webpack.stats.json`, `workbox-v4.3.1/`, `~partytown/`) is build output from a previous Gatsby deployment. Do not edit those files.

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

## Email Sign-Up Form Endpoint

The sign-up forms on `docs/index.html` and `docs/register/index.html` both POST to the same Azure Logic Apps URL:

```
https://prod-28.centralus.logic.azure.com:443/workflows/ce29f378395245b08090935fa449a6c9/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=bzJehT9XgM_dLsB_yJTaocQo_kCATKbwtOH1Y-SgSlQ
```

If this Azure workflow is ever replaced, update the `action="..."` attribute in both files.

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
