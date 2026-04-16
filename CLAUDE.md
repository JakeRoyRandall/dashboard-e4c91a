# Portfolio Dashboard

Single-file portfolio dashboard (`index.html`) hosted on GitHub Pages.

## Architecture

- **No build step.** Everything is one HTML file with inline CSS and JS.
- **Chart.js** loaded from CDN for doughnut charts.
- **Yahoo Finance v8 chart API** for live pricing (stocks and option contracts).
- **corsproxy.io** as CORS fallback since Yahoo blocks browser-origin requests.
- **GitHub Action** (`.github/workflows/update-prices.yml`) updates hardcoded fallback prices daily.

## Key data structures

- `holdings` array: All positions with share counts, cost basis, fallback prices, and option contracts.
- `categoryDef` array: Groups symbols into named categories for the pie chart. Symbols not in any category show individually.
- `buildData(prices)`: Constructs the runtime data object from holdings + fetched prices. Tracks `stkLive`/`optLive` booleans per row for the stale indicator.

## Price fetching flow

1. `fetchPrices()` receives all stock tickers + OCC option symbols
2. Tries Yahoo Finance direct, falls back to corsproxy.io
3. `buildData()` uses live prices where available, hardcoded `fp`/`opt` as fallback
4. Stale indicator (orange dot) appears on Value/P&L cells when using fallback

## Common tasks

### Add a new stock position
Add to `holdings` array with `shares`, `fp` (current price), `opt:0`, `contracts:[]`.

### Add option contracts
Add `{occ:'OCC_SYMBOL', qty:N}` to the holding's `contracts` array. OCC format: `TICKER` + `YYMMDD` + `C/P` + strike*1000 (8 digits).

### Add a new category
Add to `categoryDef` before the catch-all. Any symbol not in an explicit category shows as its own slice on the category pie chart.

### Update share counts (bought/sold)
Edit `shares` and `cost` fields on the holding.

## GitHub Pages

- **Repo:** JakeRoyRandall/dashboard-e4c91a (intentionally obscure name)
- **URL:** https://jakeroyrandall.github.io/dashboard-e4c91a/
- Deploys automatically on push to main
