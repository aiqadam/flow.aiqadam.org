# flow.aiqadam.org

Source for **[flow.aiqadam.org](https://flow.aiqadam.org)** — the public
landing for **Qadam Flow**, the inaugural AI Qadam Build project:
open-source workflow automation, built by the region for the region.

Static HTML. No framework, no bundler. The product code itself lives at
[`github.com/aiqadam/qadam-flow`](https://github.com/aiqadam/qadam-flow).

## Run it

```sh
python3 -m http.server 8765
# open http://localhost:8765/
```

You need to be online — the design system (`tokens.css`, `components.css`,
brand SVGs) is loaded live from
[`brand.aiqadam.org`](https://brand.aiqadam.org) so the styling stays in
sync with the rest of the AI Qadam umbrella.

## Edit

- Content, structure, page-specific styles → `index.html`
- Social preview image → `og-image.svg` (regenerate `og-image.png` with
  Inkscape; see [`AGENTS.md`](AGENTS.md))
- Design system (colours, components, logo SVGs) → **edit upstream** at
  [`github.com/aiqadam/brand.aiqadam.org`](https://github.com/aiqadam/brand.aiqadam.org).
  This repo references the brand site live; do not reintroduce local
  mirrors of `tokens.css`, `components.css`, or `brand/*.svg`.

## Deploy

GitHub Pages from `main`. `git push origin main` rebuilds in ~90 seconds.

## Licensing

| Material | License |
|---|---|
| Source code (HTML, inline CSS, inline JS) | MIT — see `LICENSE` |
| Project preview image (`og-image.{svg,png}`) | MIT |
| Brand assets (mark, wordmark, four-dot motif, brand teal) served from `brand.aiqadam.org` | © AI Qadam Community — see [Brand Usage Policy](https://brand.aiqadam.org/brand-use.html) |

Brand assets are not covered by MIT. If you fork this repo for something
else, swap the brand assets and the cross-origin links to the AI Qadam
design system before publishing.

## Contact

- Product / contribution questions: open an issue at
  [`aiqadam/qadam-flow`](https://github.com/aiqadam/qadam-flow)
- Brand / partnership / chapter questions: `brand@aiqadam.org`
- AI agent context when editing this repo: [`AGENTS.md`](AGENTS.md)
