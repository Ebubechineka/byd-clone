# BYD New Zealand — Static Site Clone

A self-contained static clone of the BYD New Zealand website (byd.com/nz), built for offline
reference and archival. **11 pages, 60 images, 7.3 MB total.**

This is the **v3 performance-optimized build** — 62.8% lighter than the original baseline
(21.9 MB → 8.2 MB of page weight) with no visible change to content, layout or styling.

---

## Pages

| Page | File | Purpose |
|---|---|---|
| Home | `index.html` | Hero slider, model range, brand highlights |
| Han EV | `car-han-ev.html` | Han model page |
| YANGWANG U9 Xtreme | `car-u9-xtreme.html` | U9 Xtreme model page |
| About BYD | `about-byd.html` | Brand story, technology, global presence |
| News | `news-list.html` | Article listing with live search filter |
| Search | `search.html` | Site-wide search |
| Contact | `contact-us.html` | Dealer contact details |
| Privacy Policy | `legal-privacy.html` | Legal |
| Cookie Policy | `legal-cookie-policy.html` | Legal |
| CCPA Notice | `legal-ccpa.html` | Legal |
| Data Privacy | `data-privacy.html` | Legal |

---

## Structure

```
.
├── index.html                  # 11 HTML pages, all at root
├── car-*.html
├── about-byd.html
├── news-list.html
├── search.html
├── contact-us.html
├── legal-*.html
├── data-privacy.html
├── css/
│   └── styles.css              # Single stylesheet (19.7 KB, minified)
├── js/
│   └── site.js                 # Single script (11.8 KB, minified, deferred)
├── img/                        # 60 images (WebP + JPEG + SVG)
├── CHANGES_SUMMARY.md          # Performance work log
└── .github/workflows/pages.yml # GitHub Pages deploy
```

All paths are **relative**, so the site works from any subdirectory — including a
GitHub Pages project URL such as `https://<user>.github.io/byd-clone/`.

---

## Features

- **Responsive navigation** with a mobile hamburger menu and a models dropdown
- **Hero slider** on the home page (auto-advancing, manual controls)
- **Series tabs** on model pages
- **Live news search** — filters articles as you type, with term highlighting and an empty state
- **Lazy-loaded images** — everything below the fold defers until scrolled into view

---

## Performance notes

| Optimization | Result |
|---|---|
| Image recompression | 17 MB → 6.9 MB; PNGs converted to WebP at visually-lossless quality |
| Lazy-loading | `loading="lazy"` on all images except the first per page |
| Script deferral | `defer` on `site.js` — no longer blocks HTML parsing |
| Restored assets | 14 previously-404ing header/footer images now resolve |

Gradient-heavy car renders were kept **lossless** because lossy WebP introduced visible
banding. `byd-word.gif` is byte-identical to preserve its 77-frame animation.

---

## Running locally

Any static file server works:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Opening `index.html` directly via `file://` also works, though a server is recommended
so relative paths and lazy-loading behave exactly as deployed.

---

## Deploying

The included workflow publishes to GitHub Pages on every push to `main`.

1. Push to `main`
2. In **Settings → Pages**, set **Source** to **GitHub Actions**
3. The site appears at `https://<user>.github.io/byd-clone/`

---

## Scope and limitations

This is a **static** clone. The following cannot function without a backend:

- **Form submission** — contact and enquiry forms render and validate, but POST to a
  WordPress backend that does not exist here. Submitting does nothing.
- **Server-side search** — the news filter works client-side over the articles present in
  the page; it cannot query a live article database.
- **Social and external links** — deliberately neutralised so nothing leaves the clone.

All internal links resolve within the repository. No link redirects to byd.com or any
third party.

---

## Attribution

Content, imagery and trademarks belong to BYD Company Limited. This clone is an
unaffiliated static copy created for reference and archival purposes.
