# Editing Guide (Current Live Site)

The live GitHub Pages site is served from the docs folder.

Do not edit $web. It is abandoned.

## Files You Actually Edit

Only these page files are the practical content entry points:

- docs/index.html: home page content, team section, "Get in touch" block, sign-up form text.
- docs/growing/index.html: "What we're growing" page and plant tables.
- docs/register/index.html: standalone sign-up page.
- docs/sent/index.html: success/confirmation page after signup.
- docs/404.html and docs/404/index.html: not found page.

Everything else in docs (hashed JS, maps, runtime, chunk files) is build/runtime output and usually should not be edited by hand.

## Common Edits

Address/phone appears in footer on multiple pages:

- docs/index.html
- docs/growing/index.html
- docs/register/index.html
- docs/sent/index.html
- docs/404.html
- docs/404/index.html

Main home contact block appears in:

- docs/index.html ("Get in touch" section)

Email signup endpoint appears in forms on:

- docs/index.html
- docs/growing/index.html
- docs/register/index.html

## Safe Workflow

1. Edit only the files listed above.
2. Preview in browser.
3. Commit and push.
4. GitHub Pages redeploys from docs.

## Notes

- These HTML files are mostly minified to single lines, so use find/replace for text updates.
- If you want cleaner maintenance, the next step is to split these into readable source HTML templates and copy/deploy into docs as a final publish step.
