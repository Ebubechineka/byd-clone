# BYD Clone — Performance Optimisation Report

**Original:** `byd-clone_v2` · **Optimised:** `byd-clone_v3`
**Live:** https://static.teamily.ai/sites/fafb8961-443b-4b5e-83ee-35e1bbd845ea/webpages/byd-clone_v3/index.html

---

## 1. What was slowing the site down

| Finding | Detail |
|---|---|
| **Oversized images** | 17 MB across 60 files. `s11-bg.jpg` alone was 1.6 MB; `aboutBYD-mapPc.png` 1.4 MB; `pc_e-platfrom3.png` 1.3 MB; `about_byd_pc.png` 1.1 MB. Several were photographs stored as PNG — the worst possible format for that content. |
| **No lazy-loading** | Every image on every page downloaded immediately, including ones far below the fold. `about-byd.html` pulled 6.4 MB before the visitor scrolled at all. |
| **Render-blocking script** | `js/site.js` was a plain `<script>` at the end of `<body>` with no `defer`, so it blocked parsing until fetched and executed. |
| **14 missing assets** | The header/footer injected by `site.js` referenced 14 images that were never mirrored (`BYD-Global.jpg`, `modelos-han.png`, `byd-word.gif`, the social icons, etc.). These 404'd on every page — wasted requests and broken icons. |

---

## 2. What was changed

### Images — 17 MB → 6.9 MB
- **JPEGs** recompressed at quality 78, progressive, metadata stripped. Mean pixel difference 1.36/255 — imperceptible.
- **PNGs** converted to WebP at the *highest* quality that stays visually identical. Each file was tested at q95→q82 and only accepted if mean difference < 1.0 and fewer than 0.5% of pixels differed by more than 8 levels. Typical result: 90% smaller with mean difference under 1.0.
- **Three gradient-heavy car renders** (`s4-car-blue`, `s4-car-red`, `s8-car`) failed the lossy test, so they were converted to **lossless** WebP instead — 32–37% smaller with **zero** pixel change.
- **`byd-word.gif`** (77-frame animation) left completely untouched to preserve the animation exactly.
- All dimensions, aspect ratios and alpha channels preserved.

### Lazy-loading
- `loading="lazy" decoding="async"` added to every `<img>` **except the first on each page** — the hero image stays eager so the above-the-fold render is unchanged.

### Script loading
- `js/site.js` now carries `defer`, so it no longer blocks HTML parsing. All handlers still run on `DOMContentLoaded`, so behaviour is identical.

### Missing assets restored
- All 14 previously-404ing images fetched and published. Broken icons in the header/footer now render.

### Minification
- CSS and JS were **already minified** in the source (19,716 and 11,810 bytes). A comment/whitespace pass yielded <0.1%, so no meaningful change was made — reported honestly rather than claiming a win.

---

## 3. Before / after page weight

| Page | Before | After | Saved |
|---|---:|---:|---:|
| index.html | 1,669 KB | 993 KB | **40.5%** |
| car-han-ev.html | 6,396 KB | 2,573 KB | **59.8%** |
| car-u9-xtreme.html | 2,042 KB | 1,617 KB | **20.8%** |
| about-byd.html | 6,486 KB | 1,828 KB | **71.8%** |
| news-list.html | 286 KB | 233 KB | **18.4%** |
| search.html | 48 KB | 48 KB | — |
| contact-us.html | 330 KB | 261 KB | **21.1%** |
| legal-privacy.html | 1,167 KB | 153 KB | **86.9%** |
| legal-cookie-policy.html | 1,166 KB | 152 KB | **87.0%** |
| legal-ccpa.html | 1,167 KB | 153 KB | **86.9%** |
| data-privacy.html | 1,168 KB | 153 KB | **86.9%** |
| **TOTAL** | **21,925 KB** | **8,165 KB** | **62.8%** |

Measured over HTTP with Chromium, median of 3 runs: **21,045 KB → 10,098 KB (52.0% less transferred)**. The gap between the two figures is the 14 restored images, which now actually load instead of 404ing.

---

## 4. Verification

**Visual regression** — full-page screenshots of all 11 pages, before vs after, pixel-diffed:
- 8 pages: mean difference < 0.4/255 (effectively identical)
- 3 pages (index, about-byd, car-han-ev): top 900 px identical (mean < 1.0); differences below the fold were the restored header/footer images that previously rendered as broken icons.

**Layout** — document heights unchanged on every page (index 3,979 px, about-byd 16,211 px, car-han-ev 9,853 px, etc.). No reflow, no shift.

**Broken images** — 0 on all 11 pages after scrolling.

**Features** — all pass:
```
header mounted · footer mounted · models dropdown opens · country panel opens
hero slider advances (1→2) · series tab switches to U9 · mobile menu opens
mobile models accordion · mobile menu closes · search filters as you type (4/11)
search highlights term · search empty state · news cards render (7)
```

**Links** — 0 external `href`s across all pages; every internal target resolves to a real file. Nothing points at byd.com or any third party.

---

## 5. What could not be improved

- **`byd-word.gif`** — a 77-frame animated GIF. Re-encoding risks changing the animation, so it was left byte-identical.
- **`s4-car-blue/red`, `s8-car`** — lossy WebP introduced visible banding in the smooth gradients, so lossless was used instead (smaller gain, zero visual change).
- **CSS/JS minification** — already minified upstream; no further gain available.
- **Browser caching** — controlled by the CDN's response headers, not by anything in the files. Cannot be set from a static clone.
