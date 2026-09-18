# FBI Crypto Bank — V6 Final / Professor Edition

A browser-based capital-management and banking-system research environment for economics coursework. It models portfolio valuation, double-entry journals, trade workflow, risk analytics, audit events, reporting and hypothetical compounding scenarios.

## Repository layout

- `public/` — source web application and PWA assets
- `netlify/functions/crypto-prices.js` — optional serverless market proxy for Netlify
- `build.mjs` — deterministic production build (`public/` → `dist/`)
- `.github/workflows/ci.yml` — GitHub Actions validation/build check
- `.github/workflows/pages.yml` — optional GitHub Pages deployment
- `netlify.toml` — Netlify build, function and routing configuration

## Local development

Requirements: Node.js 18+.

```bash
npm run check
npm run build
```

The static production files are written to `dist/`. Because this project has no npm runtime dependencies, a fresh clone can build without installing a dependency tree.

For local preview, serve `dist/` with any static HTTP server. Do not open `index.html` directly from `file://`; PWA/service-worker behavior requires HTTP(S).

## GitHub

Push the repository to GitHub with `main` or `master` as the deployment branch. GitHub Actions will run the CI workflow automatically.

To use GitHub Pages, enable **Settings → Pages → Source: GitHub Actions**. The Pages workflow builds `dist/` and deploys it automatically.

GitHub Pages does not execute Netlify Functions. The client therefore treats `/api/market` as the preferred market endpoint and can fall back to a direct public CoinGecko request, then the last cached snapshot, then built-in reference data. Netlify deployments keep using the server-side `crypto-prices` function.

## Netlify

- Build command: `npm run build`
- Publish directory: `dist`
- Functions directory: `netlify/functions`
- Market route: `/api/market`

Deploying from the GitHub repository to Netlify is supported without changing the source tree.

## Market synchronization

The client uses a bounded request timeout. It first attempts the same-origin `/api/market` route. If that endpoint is unavailable (for example on GitHub Pages), it attempts the public CoinGecko reference API. If the provider is unavailable, the app uses the last good cached market snapshot, then a built-in reference set. The UI reports the source and update time instead of remaining indefinitely in a waiting state.

## Capital model

- Starting research capital: **€96,453.00**
- Actual NAV: cash + market-valued positions
- Scenario NAV: separate mathematical compounding model
- Scenario rates: **1%, 2%, 3% daily**
- Scenario formula: `Future = Initial × (1 + rate)^days`

The 1–3% figures are hypothetical scenario assumptions for analysis, not guaranteed investment returns.

## Accounting

Opening capital is posted to a balanced opening journal. BUY, SELL and fee events create journal records. The trial balance is computed from the ledger rather than hard-coded.

## PWA

The project includes a manifest, install button and versioned service worker. API requests are explicitly excluded from service-worker caching.

## Security note

The PIN is a local browser lock, not server-side authentication. Do not treat it as protection for sensitive financial credentials or real banking access. No API secrets are required by the included market integration.
