# Agent context — flow.aiqadam.org

> If you're an AI assistant landing in this repo, start here. This is the
> condensed mental model you need before editing.

This repo is the source for **`flow.aiqadam.org`** — the public landing for
**Qadam Flow**, the inaugural AI Qadam Build project: open-source workflow
automation, built by the region for the region. Static HTML — no
framework, no bundler, no toolchain.

The product code itself lives in a separate repo:
[`github.com/aiqadam/qadam-flow`](https://github.com/aiqadam/qadam-flow).
This repo is just the marketing/info site that points to it.

## Architecture

One page, no routing:

```
index.html          → the whole landing
```

Sections inside `index.html` (single-page anchored nav):

- *(hero)*          — name, tagline, primary CTA, dashboard screenshot
- `#why`            — positioning ("region builds its own automation")
- `#tour`           — templates (252-template library)
- `#builder`        — flow builder, Ollama AI Webhook hero screenshot
- `#integrations`   — 238 qadams, Stripe trigger, stats tiles
- `#ai`             — AI providers + MCP, side-by-side dual shot
- `#runs`           — run inspector
- `#self-host`      — multi-tenant admin + one-command quickstart
- `#status`         — honest framing of what's not yet built
- `#join`           — how to contribute
- *(cta-final)*     — closing CTA with sign-in screenshot

There is no second page, no blog, no docs site. Long-form lives on GitHub.

## Design system — loaded live from the brand site

This repo holds **no CSS, no logo SVGs, no design tokens**. All styling
comes from [`brand.aiqadam.org`](https://brand.aiqadam.org) at runtime:

```html
<link rel="stylesheet" href="https://brand.aiqadam.org/tokens.css" />
<link rel="stylesheet" href="https://brand.aiqadam.org/components.css" />
<link rel="icon" type="image/svg+xml" href="https://brand.aiqadam.org/brand/logo-mark.svg" />
<!-- and so on for logo-full.svg in the OG tags and aiq-build-badge -->
```

Why: zero drift. Any update to tokens, components, or brand SVGs at
`brand.aiqadam.org` flows through to `flow.aiqadam.org` instantly,
without a mirror-and-commit step in this repo.

**Consequences:**

- `brand.aiqadam.org` is treated as a **stable, read-only reference**.
  Do not propose changes to it (no PRs adding components, no edits to
  `components.css` / `tokens.css`). It stays stable so every consumer
  site (this one, `build.aiqadam.org`, etc.) coheres against the same
  snapshot.
- Page-specific composition — hero layout, split sections, screenshot
  frames, dual-shot rows, feature grids, status cards — lives in the
  `<style>` block inside `index.html`. That is fine and expected: it is
  composition on top of the design system, not a replacement of it.
- Never invent new colours, fonts, or component primitives. Only use
  existing tokens (`--primary`, `--card`, `--border`,
  `--muted-foreground`, …) and existing brand components (`.btn`,
  `.card`, `.badge`, `.mockup`, `.cover`, …).
- Don't reintroduce a local `tokens.css`, `components.css`, or `brand/`
  directory — that defeats the live-link design.
- If you're previewing locally and the network is unreachable, the page
  will render unstyled. That's expected — open the live brand site or
  work online.

**Heads up — github-corner and aiq-build-badge are inlined.** On the
brand site these two live in `docs.css` (which the brand site treats as
"page chrome shared by docs pillars"), not `components.css`. We only load
`tokens.css` and `components.css` cross-origin, so the github-corner and
aiq-build-badge styles are **inlined verbatim** inside the `<style>` block
of `index.html`. Keep them in sync with the brand site by hand if the
brand snapshot ever shifts.

## File layout

```
.
├── index.html              ← the landing (only HTML page)
│
├── assets/
│   └── product/            ← 9 product screenshots (1280×633 PNG)
│                              + SCREENSHOTS.md (recapture recipe)
│
├── run.sh                  ← one-line installer — served at
│                              https://flow.aiqadam.org/run.sh
│                              (curl … | sh quickstart target)
│
├── og-image.svg            ← 1200×630 social preview — source
├── og-image.png            ← 1200×630 social preview — referenced from <meta og:image>
│
├── CNAME                   ← flow.aiqadam.org — GitHub Pages custom domain
├── robots.txt              ← allow all + explicit AI crawlers + sitemap pointer
├── sitemap.xml             ← single URL (the landing)
├── llms.txt                ← structured markdown index for LLM agents
│
├── LICENSE                 ← MIT — code only
├── README.md
├── AGENTS.md               ← this file
└── CLAUDE.md               ← symlink → AGENTS.md
```

That's the whole repo. No CSS, no logo SVGs, no scripts directory beyond
the single `run.sh` quickstart installer.

## Project-specific assets (local)

Two narrow exceptions where assets live in this repo rather than being
referenced from `brand.aiqadam.org`:

### 1. og-image (social preview)

Social platforms (Facebook, LinkedIn, Telegram, WhatsApp) don't render SVG
preview cards reliably, and the brand site's own `og-image.svg` is
specifically branded "AI Qadam Brand Guidelines" — wrong for Qadam Flow.

- `og-image.svg` — the source (footprint mark on the right, Qadam Flow
  title + tagline on the left, brand teal accent stripe, dark surface).
  Mirrors the layout convention from `brand.aiqadam.org/brand/og-image.svg`.
- `og-image.png` — the 1200×630 PNG rendered from the SVG via Inkscape.

To regenerate the PNG after editing the SVG:

```sh
/Applications/Inkscape.app/Contents/MacOS/inkscape \
  --export-type=png \
  --export-filename=og-image.png \
  --export-width=1200 --export-height=630 \
  og-image.svg
```

Commit both files. The PNG is what `<meta property="og:image">` points at.

### 2. Product screenshots (`assets/product/`)

Nine production-stack screenshots of the Qadam Flow product, captured
from `qadam-flow:latest` running locally and used throughout the
landing's story arc (hero, templates, builder, integrations, AI / MCP,
runs, self-host, sign-in CTA).

- Lives only here. Originally produced in `qadam-flow/landing-export/`,
  but that staging directory was removed once the landing shipped —
  this repo is now the source of truth.
- Resolution: 1280×633 (rendered at ~50% width on desktop — sharp enough
  for retina at that scale without a 2× recapture).
- File naming: `01-signin.png` through `09-platform-users.png` matches
  the order documented in `assets/product/SCREENSHOTS.md`.

To re-capture after the product changes, follow the reproduction recipe
in `assets/product/SCREENSHOTS.md` (run `qadam-flow:latest` locally,
build the Ollama-webhook flow via the API, screenshot at 1280×640 via
agent-browser), then overwrite the PNGs in place.

### 3. Quickstart installer (`run.sh`)

The landing's quickstart promises
`curl -fsSL https://flow.aiqadam.org/run.sh | sh` — `run.sh` therefore
has to live at the repo root so GitHub Pages serves it at that URL.

- This is a synced copy of `qadam-flow/run.sh` from the product repo.
- The script itself downloads `docker-compose.yml` from
  `raw.githubusercontent.com/aiqadam/qadam-flow/main/docker-compose.yml`
  — we do not host that file here.
- Keep this in sync with the upstream `qadam-flow/run.sh` when it
  changes. The script is the public install contract, so changes
  shouldn't be silent.

## Conventions

- **Brand assets are immutable.** Never recolour, redraw, or substitute the
  mark, wordmark, or four-dot motif. Never use brand teal as a generic
  accent — it is the brand colour. The full policy is at
  [`brand.aiqadam.org/brand-use.html`](https://brand.aiqadam.org/brand-use.html).
- **Tokens are normative.** All colours come from `tokens.css` semantic
  variables (`--primary`, `--foreground`, `--card`, etc.). Don't write
  raw hex except the single `<meta name="theme-color" content="#3CA29E">`
  declaration.
- **Always pull design-system assets cross-origin.** `tokens.css`,
  `components.css`, `brand/logo-mark.svg`, `brand/logo-full.svg` are loaded
  via `https://brand.aiqadam.org/...` URLs. Never copy them into this repo.
- **Chrome elements are mandatory.** Every page must carry: the GitHub
  corner ribbon (links to `github.com/aiqadam/qadam-flow`), the
  `aiq-build-badge` in the footer (links to `https://build.aiqadam.org`),
  and the theme-toggle button in the header. These signal the project's
  position inside the AI Qadam umbrella.
- **Per-page meta is non-negotiable.** Every public page must carry: unique
  `<meta name="description">`, OpenGraph block (`og:type`, `og:site_name`,
  `og:title`, `og:description`, `og:url`, `og:image`, `og:locale=ru_RU`),
  Twitter Card (`twitter:card=summary_large_image`, `twitter:title`,
  `twitter:description`, `twitter:image`), `<link rel="canonical">`, and
  `<meta name="theme-color" content="#3CA29E">`.
- **English in content, Russian in conversation.** The site copy at
  `flow.aiqadam.org` is in English, matching the brand site and the
  GitHub repo — localisation into Russian / Uzbek / Kazakh comes later
  via the "Multilingual by default" principle, once the product UI
  catches up. The user (Binali) writes to AI agents in Russian. Reply
  in Russian.
- **Don't translate brand-name spellings.** "AI Qadam", "Qadam Flow" in
  Title Case, two words, every time. Never "AIQadam", "ai qadam", or
  hyphenated/phonetic variants.
- **No framework, no bundler.** The site is static on purpose. Don't add a
  build step, a CSS preprocessor, or a JS framework. Deploy must work as a
  `git push` to GitHub Pages with zero CI.

## Build & local dev

It's static HTML — no build:

```sh
python3 -m http.server 8765
# open http://localhost:8765/
```

The page pulls CSS and SVGs from `https://brand.aiqadam.org` over the
network, so you need to be online for it to render correctly.

## Deploy

The site is hosted on **GitHub Pages** from the `main` branch root:

- Custom domain: `flow.aiqadam.org` (set via the `CNAME` file in repo root)
- HTTPS enforced via GitHub Pages settings

Deploy is `git push origin main`. GitHub Pages auto-rebuilds in ~90 seconds.

After push, check Pages status with
`gh api repos/aiqadam/flow.aiqadam.org/pages`.

## Licensing

| Material | License | File |
|---|---|---|
| Source code (HTML, inline CSS, inline JS) | MIT | `LICENSE` |
| Brand assets (mark, wordmark, four-dot motif, brand teal) | © AI Qadam — usage policy | served from `brand.aiqadam.org` |

Brand assets are **not** covered by MIT. They are governed by the AI Qadam
Brand Usage Policy. If you fork this repo to build something else, swap
the brand assets out before publishing — and don't keep the
`brand.aiqadam.org` CSS links pointing at the AI Qadam design system.

## Common mistakes to avoid

- Don't reintroduce a local `tokens.css`, `components.css`, or `brand/`
  directory. The whole point of the cross-origin setup is to remove drift.
- Don't redraw, recolour, or AI-generate variants of the mark, wordmark,
  or four-dot motif. Reference the SVGs from `brand.aiqadam.org` only.
- Don't claim "AI Qadam Build" status for forks without recognition from
  community leads (council of country leads + Global Board).
- Don't introduce a JS framework, bundler, CSS preprocessor, or build
  toolchain. Static is the design.
- Don't add new colour tokens or font families. The palette is one teal +
  neutrals; the families are Geist / Inter / JetBrains Mono.
- Don't skip per-page meta (description, OG, Twitter Card, canonical,
  theme-color) when adding any new HTML page.
- Don't break the chrome contract: GitHub corner + `aiq-build-badge` +
  theme-toggle must stay on every page.
- Don't push raw secrets or local configs. `.claude/settings.local.json`
  is gitignored; check for tokens before committing.
