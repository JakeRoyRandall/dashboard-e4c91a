# Portfolio Dashboard

Live portfolio dashboard with real-time stock and option pricing via Yahoo Finance.

**Live URL:** https://jakeroyrandall.github.io/dashboard-e4c91a/

## How it works

Single `index.html` file hosted on GitHub Pages. On each page load:

- Fetches live stock prices for all equity positions
- Fetches live option prices for all 18 LEAPS contracts (using OCC symbols)
- Falls back to cached prices if Yahoo Finance is unreachable (orange dot indicator)
- Cached fallback prices auto-update daily via GitHub Action (4pm ET, weekdays)

## Holdings structure

All portfolio data lives in the `holdings` array in `index.html`:

```js
{symbol:'GOOG', name:'Alphabet', cost:79642.65, shares:200, fp:335.22, opt:93036, contracts:[
  {occ:'GOOG281215C00400000', qty:6},
  {occ:'GOOG271217C00300000', qty:6}
]}
```

| Field | Description |
|-------|-------------|
| `symbol` | Ticker symbol |
| `cost` | Total cost basis (stock + options) |
| `shares` | Number of stock shares held |
| `fp` | Fallback stock price (updated daily by Action) |
| `opt` | Fallback total option value (updated daily by Action) |
| `contracts` | Option contracts: OCC symbol + quantity |

## Adding or changing positions

Edit the `holdings` array in `index.html`:

- **Add stock:** Add entry with `shares`, `fp` (current price), `opt:0`, `contracts:[]`
- **Add options:** Add `{occ:'SYMBOL_YYMMDD_C_STRIKE', qty:N}` to the `contracts` array
- **New category:** Add to `categoryDef` array. Uncategorized symbols show individually on the pie chart.

OCC option symbol format: `SYMBOL` + `YYMMDD` + `C` (call) or `P` (put) + strike price x1000 padded to 8 digits.
Example: AAPL July 17 2026 $260 Call = `AAPL260717C00260000`

## GitHub Action

`.github/workflows/update-prices.yml` runs daily after market close to update the hardcoded fallback prices (`fp` and `opt` fields). Can also be triggered manually from the Actions tab.
