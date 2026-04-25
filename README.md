# Frank A. Smith Nurseries Website

Live site: **[fasnurseries.com](https://fasnurseries.com)**

---

## Quick Start — Editing Content

**For non-technical edits** (address, phone, plant names, descriptions):
- See [docs/EDITING_GUIDE.md](docs/EDITING_GUIDE.md)

**For technical setup or infrastructure questions**, scroll to the sections below.

---

## What's in This Repo

| Folder | Purpose |
|--------|---------|
| `docs/` | **Live website root** — all HTML, CSS, images, and assets. Changes here go live automatically. |
| `.git/` | Git version control |

That's it. The site is a simple static HTML deployment using GitHub Pages.

---

## How It Works

1. **Edit files** in `docs/` (HTML pages, contact info, plant inventory, etc.)
2. **Commit and push** to `main` branch
3. **Site updates** automatically within 1–2 minutes via GitHub Pages

No build step. No CI/CD pipeline. No framework. Plain HTML + CSS + inline JavaScript.

---

## Site Structure

```
docs/
├── index.html                         ← Home page
├── growing/index.html                 ← Plant availability
├── register/index.html                ← Sign-up form
├── sent/index.html                    ← Sign-up confirmation
├── 404.html & 404/index.html          ← Error pages
├── styles.6d2842a8ac114ab1800e.css   ← Main stylesheet
├── favicon-32x32.png
├── icons/                             ← Apple touch icons
├── static/                            ← Images & Font Awesome
│   ├── banner-*.jpg
│   ├── jr-*.jpg, darren-emerick-*.jpg (team photos)
│   ├── plant photos (*.jpg)
│   └── font files (fa-*.ttf, fa-*.woff2, etc.)
├── CNAME                              ← Custom domain config
├── .nojekyll                          ← Disables Jekyll processing
└── EDITING_GUIDE.md                   ← Content editing instructions
```

---

## Deployment

**Automatic:** Push to `main` → site updates in 1–2 minutes.

```bash
# Make changes to docs/
git add docs/
git commit -m "Update plant availability"
git push origin main
```

**Custom domain:** `fasnurseries.com` is configured in `docs/CNAME` and DNS.

---

## Editing Workflow

### Simple Edits (Address, Contact, Plant Names)

Use Find & Replace (Ctrl+H) in your editor:

1. Open `docs/index.html`, `docs/growing/index.html`, etc.
2. Search for the text to change (e.g., `(770) 454-7994`)
3. Replace it
4. Commit and push

**Note:** HTML files are minified (one long line) — copy the exact text from the source and replace it exactly.

### Adding/Removing Plants

See [docs/EDITING_GUIDE.md — Plant Availability Page](docs/EDITING_GUIDE.md#plant-availability-page-docsgrowingindexhtml)

### Replacing Images

1. Add new image to `docs/static/`
2. Update the `src` or `url()` reference in the corresponding HTML or CSS file
3. Old images can stay (they don't hurt) or be deleted

---

## Key Details

| Item | Value | Edit Location |
|------|-------|---------------|
| Address | 1310 Austell Road SE, Marietta, GA 30008 | All 6 HTML files (footer) |
| Main phone | (770) 454-7994 | All 6 HTML files (footer) |
| JR's cell | (770) 352-4407 | `docs/index.html` only |
| Darren's cell | (770) 530-5078 | `docs/index.html` only |
| Email signup URL | Azure Logic Apps endpoint | `docs/index.html` & `docs/register/index.html` |
| Custom domain | `fasnurseries.com` | `docs/CNAME` |

**Important:** Contact details are hardcoded in each HTML file individually — there's no template. If phone/address changes, update all 6 pages.

---

## Local Testing

Serve the site locally to preview changes before pushing:

```bash
# Python 3
cd docs
python -m http.server 8080

# Open http://localhost:8080 in your browser
```

Images and styles will load correctly (unlike opening `index.html` directly).

---

## File Maintenance

**These files should NOT be edited by hand:**
- JavaScript that's minified into HTML
- Font/image hashes in filenames (these are content-based and auto-managed)

**Safe to ignore:**
- Old image files in `docs/static/` (if unused, they don't cause problems)
- The `.git/` folder

---

## Infrastructure Notes

Currently **GitHub Pages** serves the site from the `docs/` folder. No CDN, no serverless, no database — just static HTML.

### Future: Azure Static Web Apps

The `README.md` previously included Azure Static Web Apps migration steps (deleted in April 2026). If you want to migrate away from GitHub Pages to Azure SWA for better CDN, custom headers, or serverless API support, contact the project maintainer for the archived infrastructure docs.

---

## Browser Compatibility

- Modern browsers (Chrome, Firefox, Safari, Edge)
- Mobile-responsive design
- Inline JS for menu toggle (no jQuery, no external framework)

---

## Support

For content/editing questions: See [docs/EDITING_GUIDE.md](docs/EDITING_GUIDE.md)  
For technical/infrastructure issues: Open an issue on GitHub or contact the maintainer.
