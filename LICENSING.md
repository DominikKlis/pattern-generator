# Font licensing

## Current status: **not licensed for the web**

The app is designed for Cassis (Frere-Jones Type). The only licence in hand is the
**Frere-Jones Type Desktop EULA v3.1** shipped with the font purchase
(`Cassis-10092/FJT Desktop EULA v3.1.pdf`). That licence does **not** cover this app's
use case.

Relevant clauses:

| Clause | What it says |
| --- | --- |
| §4.1 Web use | Styling website text via `@font-face` requires a separate **Web Font licence**, and only the WOFF/WOFF2 files supplied with it may be used. |
| §4.3 Distribution | The font files may not be distributed to third parties. Serving them to site visitors — inline, from `fonts/`, or from a CDN — is distribution. |
| §4.4 Modification | The files may not be converted to another format, so we cannot make our own WOFF2 from the desktop OTFs. |
| §3.2 Document embedding | Embedding is allowed only when the font is subset **and** the document is distributed so the font cannot be extracted. Neither holds for base64 in an HTML page or in an exported SVG. |
| §4.2 AI use | The fonts and typeface designs may not be used for machine learning or AI training. |

Hosting the OTFs at a public URL instead of embedding them changes nothing: it is the
same web use and the same distribution.

## Current build: fonts ARE embedded — a knowing exception

The committed `index.html` inlines all seven Cassis OTFs as base64 in
`#cassis-fonts-data`. This was a deliberate decision, taken with the above clauses
known, on the grounds that the page is a small test/demo.

Be clear about what that means: the EULA has **no exemption for demos, tests or
low-traffic sites**, and publishing this file (GitHub Pages included) distributes
complete, unsubsetted, trivially extractable font files to every visitor. This is a
licence breach we are choosing to carry, not one the licence permits. Buy the web
licence before this becomes anything more than a demo.

## How the fonts resolve

`registerCassisFonts()` takes the first hit of three:

1. `window.CASSIS_OTF_B64` — inlined by `#cassis-fonts-data`. **Current demo build.**
2. `window.CASSIS_FONT_URLS` — fetched from `fonts/`, a **git-ignored** directory that
   exists only on a machine holding the desktop licence. (`Cassis-10092/` and the
   original zip are ignored too.)
3. Neither — **Montserrat** (SIL Open Font License, fine for the web), via the CSS
   stack for display and the jsdelivr TTF for Lottie export.

Every Cassis path fails soft, so each step down is safe.

## Reverting to a licence-clean page

Delete the single `<script id="cassis-fonts-data">…</script>` element from
`index.html`. Nothing else changes: the loader falls through to `fonts/` locally and to
Montserrat everywhere else. Verified — a checkout with no `fonts/` renders Montserrat
with zero console errors.

In that mode `file://` also shows Montserrat, because `fetch` cannot read local files.
Serve over HTTP to see Cassis:

```bash
python3 -m http.server 8765   # then open http://localhost:8765/index.html
```

## Caveat on SVG export

`embedFontsInSvg()` inlines the OTFs of every weight used into the exported SVG, so each
export is itself a distributable file with extractable font data — the same breach,
travelling further. Keep exports internal until a web/embedding licence is in place.

## To ship Cassis publicly

Buy a **Web Font licence** for Cassis from Frere-Jones Type
(`licensing@frerejones.com`), take the WOFF2 files they supply, drop them into `fonts/`,
point the manifest at them, un-ignore the directory, and delete this warning.
