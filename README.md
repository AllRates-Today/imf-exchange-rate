# International Monetary Fund Exchange Rates API — imf-exchange-rate

[![npm version](https://img.shields.io/npm/v/imf-exchange-rate.svg)](https://www.npmjs.com/package/imf-exchange-rate)
[![license](https://img.shields.io/npm/l/imf-exchange-rate.svg)](https://github.com/AllRates-Today/imf-exchange-rate/blob/main/LICENSE)
[![zero dependencies](https://img.shields.io/badge/dependencies-0-brightgreen.svg)](https://www.npmjs.com/package/imf-exchange-rate)
[![TypeScript](https://img.shields.io/badge/TypeScript-types%20included-3178C6.svg)](https://www.typescriptlang.org/)


**Official International Monetary Fund (IMF) daily exchange rates for Node.js and TypeScript. The published international organisation rates behind tax filings, customs valuations, audits, and compliant invoicing — not market estimates, but the numbers International Monetary Fund itself prints, every business day.**

## 🚀 Why this client?

- 🏛️ **Official published rates** — International Monetary Fund's own table, with the publisher's own `rate_date` on every response
- 📅 **History back to 2003** — point-in-time tables and daily series for any past date
- 🔀 **Published vs derived, always flagged** — computed inverse/cross pairs carry `derived: true`, never mixed with official prints
- ⚡ **Zero dependencies** — pure ESM + CJS over global `fetch`; Node 18+, Bun, Deno, and edge runtimes
- 🔷 **Type-safe** — full TypeScript definitions shipped with the package
- 🧾 **Compliance-grade metadata** — `rate_type`, publication date, and source disclaimer on every response

> **Official rate, not mid-market:** every value here is a number International Monetary Fund itself published, fixed once printed and carrying the international organisation's own `rate_date` — what filings and audits require. Need the live mid-market rate for pricing or display instead? Use the [mid-market API](https://allratestoday.com/docs/) or [`@allratestoday/sdk`](https://www.npmjs.com/package/@allratestoday/sdk). The two can diverge by several percent.

## 🔑 Get your API key

**Paid plans only.** This source is not on the free plan or any keyless endpoint — the latest table and the history both need a key on a [paid plan](https://allratestoday.com/pricing/). Create an account at [allratestoday.com/register](https://allratestoday.com/register), then upgrade.

## 📦 Installation

```bash
npm install imf-exchange-rate
```

```bash
yarn add imf-exchange-rate
```

```bash
pnpm add imf-exchange-rate
```

Requires Node 18+ (global `fetch`); also runs on Bun, Deno and edge runtimes. Also published under the org scope as [`@allratestoday/imf-exchange-rate`](https://www.npmjs.com/package/@allratestoday/imf-exchange-rate) — same code, same versions.

## 🏁 Quick start

```js
import { getRate } from 'imf-exchange-rate';

const pair = await getRate('USD', 'CNY', { apiKey: 'art_live_...' });
console.log(pair.rate, pair.rate_date); // the official International Monetary Fund rate, on the international organisation's own date
```

## 📚 API reference

- [Latest pair rate](#latest-pair-rate) — one pair from the latest published table
- [Full published table](#full-published-table) — everything the international organisation printed, in one call
- [Table for a date](#table-for-a-date) — the official table for an invoice or filing date
- [Daily time series](#daily-time-series) — one pair across a date range

---

### Latest pair rate

Paid plans. Pairs the international organisation does not print directly are resolved from its table and flagged (see *Published vs derived rates* below).

```js
const pair = await getRate('USD', 'CNY', { apiKey: 'art_live_...' });
```

**Response:**

```javascript
{
  bank: 'imf',
  name: 'International Monetary Fund',
  rate_date: '2026-10-27',   // International Monetary Fund's own publication date
  source: 'USD',
  target: 'CNY',
  rate: 6.6684,
  rate_type: 'reference',
  derived: false,
  method: 'published',
  disclaimer: 'Official rates as published by the named central bank. On weekends/holidays the most recent published rate_date is returned.'
}
```

### Full published table

Paid plans. The complete table for the latest publication date.

```js
import { getLatestRates } from 'imf-exchange-rate';

const table = await getLatestRates({ apiKey: 'art_live_...' });
console.log(table.rate_date, table.rates.length);
```

**Response:**

```javascript
{
  bank: 'imf',
  name: 'International Monetary Fund',
  rate_date: '2026-10-27',
  rates: [
    { "base": "USD", "quote": "CNY", "type": "reference", "value": 6.6684 },
    // … the rest of the published table (36 currencies vs USD)
  ],
  disclaimer: '…'
}
```

### Table for a date

Paid plans. The official table for any date since 2003 — weekends and holidays return the most recent published date, flagged via `published_on_requested_date`, which is exactly the in-force rate a filing needs.

```js
import { getRatesForDate } from 'imf-exchange-rate';

const day = await getRatesForDate('2026-06-30', { apiKey: 'art_live_...' });
// Optionally narrow to one pair:
const one = await getRatesForDate('2026-06-30', { apiKey: 'art_live_...', source: 'USD', target: 'CNY' });
```

**Response:**

```javascript
{
  bank: 'imf',
  requested_date: '2026-06-30',
  rate_date: '2026-06-30',                // the date actually published
  published_on_requested_date: true,      // false when a weekend/holiday fell back
  rates: [ /* the full table for that date */ ],
  disclaimer: '…'
}
```

### Daily time series

Paid plans. One resolved rate per publication date — ready for charting, revaluation runs, or audit workpapers.

```js
import { getHistory } from 'imf-exchange-rate';

const series = await getHistory(
  { source: 'USD', target: 'CNY', from: '2026-01-01', to: '2026-10-27' },
  { apiKey: 'art_live_...' }
);
```

**Response:**

```javascript
{
  bank: 'imf',
  source: 'USD',
  target: 'CNY',
  from: '2026-01-01',
  to: '2026-10-27',
  count: 152,
  rates: [
    // one entry per publication date
    { date: '2026-10-27', rate: 6.6684, rate_type: 'reference', derived: false, method: 'published' },
    // …
  ],
  disclaimer: '…'
}
```

Pass `{ symbol: 'CNY' }` instead of `source`/`target` to get the raw published rows for one currency (all rate types, no pair resolution).

---

## 🗺️ Currencies covered

International Monetary Fund currently publishes rates covering **36 currencies** against the USD (as of the latest table):

🇦🇪 `AED` · 🇦🇺 `AUD` · 🇧🇳 `BND` · 🇧🇷 `BRL` · 🇧🇼 `BWP` · 🇨🇦 `CAD` · 🇨🇭 `CHF` · 🇨🇱 `CLP` · 🇨🇳 `CNY` · 🇨🇿 `CZK` · 🇩🇰 `DKK` · 🇩🇿 `DZD` · 🇪🇺 `EUR` · 🇬🇧 `GBP` · 🇮🇱 `ILS` · 🇮🇳 `INR` · 🇯🇵 `JPY` · 🇰🇷 `KRW` · 🇰🇼 `KWD` · 🇲🇺 `MUR` · 🇲🇽 `MXN` · 🇲🇾 `MYR` · 🇳🇴 `NOK` · 🇳🇿 `NZD` · 🇴🇲 `OMR` · 🇵🇪 `PEN` · 🇵🇭 `PHP` · 🇵🇱 `PLN` · 🇶🇦 `QAR` · 🇸🇦 `SAR` · 🇸🇪 `SEK` · 🇸🇬 `SGD` · 🇹🇭 `THB` · 🇹🇹 `TTD` · 🇺🇾 `UYU` · `XDR`

## 🏛️ Source

The International Monetary Fund publishes representative exchange rates for about 40 currencies every business day — the rates member central banks report to the Fund, in currency units per U.S. dollar (U.S. dollars per unit for the euro, pound, Australian and New Zealand dollars and pula) — together with each currency's value in SDRs. They are the rates the IMF itself uses for its operations and the reference behind the SDR valuation. The API serves the dollar table under base USD and the SDR values as currency/XDR pairs, with history back to 2003. Paid plans only.

- Publisher's own page: [Exchange Rate Archives by Month](https://www.imf.org/external/np/fin/data/param_rms_mth.aspx) · [www.imf.org](https://www.imf.org)
- Publication: every business day; the exact schedule, freshness status and any current delay are on the [International Monetary Fund rates page](https://allratestoday.com/central-bank-rates-api/imf/)
- Values are stored unmodified, with the publisher's own `rate_date` on every row — see the [methodology](https://allratestoday.com/official-rates-methodology/)

## 🧭 Reading the numbers

- `value` is always **quote currency per 1 unit of base currency** (`base: "EUR", quote: "USD", value: 1.15` means 1 EUR = 1.15 USD).
- The International Monetary Fund prints pairs in both directions: most currencies as **units per 1 USD** (`base: "USD", quote: "CNY"`), the euro, pound, Australian and New Zealand dollars and pula as **USD per 1 unit** (`base: "EUR", quote: "USD"`), and every currency's value in SDRs as `base: "<CCY>", quote: "XDR"`. Read `base` and `quote` on every row.
- Need the other way round? Ask `getRate(target, source)` and the API inverts or crosses for you, flagged `derived: true` — never divide a published rate yourself in a compliance workflow.
- `rate_type` tells you which of the international organisation's series a row belongs to (`reference` here); some publishers print buy/sell or several fixings for the same pair.

## 🧩 ERP & accounting systems

Loading the official International Monetary Fund rate into an accounting system is a supported workflow, not a hack. Step-by-step guides with the direction each system expects:

- [Dynamics 365 Business Central](https://allratestoday.com/docs/integrations/business-central/) — built-in Currency Exchange Rate Service, no code
- [Xero](https://allratestoday.com/docs/integrations/xero/) · [QuickBooks Online](https://allratestoday.com/docs/integrations/quickbooks/) · [SAP S/4HANA and ECC](https://allratestoday.com/docs/integrations/sap/) · [Odoo](https://allratestoday.com/docs/integrations/odoo/)

The same keyed endpoints return `?format=csv`, `?format=xml` and `?format=xlsx`, and accept the key as `?api_key=` on the URL for importers that cannot send headers:

```bash
curl "https://allratestoday.com/api/v1/central-bank/imf/latest?format=xml&api_key=art_live_..."
```

## 🤖 AI agents

- MCP server: `npx -y @allratestoday/central-bank-mcp` (stdio) or the hosted endpoint `https://allratestoday.com/api/mcp` — with a paid key: tools for official rates, history, cross-bank comparison and publication calendars
- Machine-readable site guide: [llms.txt](https://allratestoday.com/llms.txt) · [for-ai-agents](https://allratestoday.com/for-ai-agents/)

## ⚖️ Published vs derived rates

If International Monetary Fund does not print a pair directly, the API resolves it from the international organisation's own table and says so — official and computed values are never confused:

| `method` | `derived` | Meaning |
| --- | --- | --- |
| `published` | `false` | The international organisation printed this pair directly |
| `inverse` | `true` | Computed as 1 ÷ the published opposite direction |
| `cross` | `true` | Computed via USD from two published rates |

## 🛡️ Error handling

Errors are thrown as `Error` with `status` (HTTP code) and `body` (the API's JSON error) attached:

```js
try {
  const pair = await getRate('USD', 'XXX', { apiKey: 'art_live_...' });
} catch (err) {
  console.log(err.message); // human-readable reason
  console.log(err.status);  // e.g. 404
}
```

| Status | Meaning |
| ------ | ------- |
| — | Missing `apiKey` (thrown before any request) |
| `400` | Malformed date or parameters |
| `401` | Invalid API key |
| `403` | This source needs a [paid plan](https://allratestoday.com/pricing/) (every endpoint) |
| `404` | Pair or date range not covered by International Monetary Fund |
| `429` | Monthly quota exceeded |

## 🔷 TypeScript

Full definitions ship with the package — no `@types` install:

```ts
import type { LatestRates, PairRate, DatedRates, RateEntry, HistoryQuery, RequestOptions } from 'imf-exchange-rate';
```

## 📦 CommonJS

```javascript
const { getRate } = require('imf-exchange-rate');

getRate('USD', 'CNY', { apiKey: 'art_live_...' }).then((pair) => console.log(pair.rate));
```

## 💡 Quota tips

- Rates change once per business day — cache the published table locally and a small monthly quota goes a long way.
- Every request counts toward your AllRatesToday quota, shared across all AllRatesToday endpoints on your key.

## 📖 Methods reference

| Method | Plan | Description |
| ------ | ---- | ----------- |
| `getRate(source, target, { apiKey })` | Paid | Latest rate for one pair, resolved from the published table |
| `getLatestRates({ apiKey })` | Paid | The international organisation's full latest published table |
| `getRatesForDate(date, { apiKey, source?, target? })` | Paid | The official table (or one pair) for a YYYY-MM-DD date |
| `getHistory({ symbol \| source+target, from?, to? }, { apiKey })` | Paid | Daily series since 2003 |

## 🔗 Links

- [International Monetary Fund rates page](https://allratestoday.com/central-bank-rates-api/imf/) — publication cadence, FAQ
- [All central bank sources](https://allratestoday.com/central-bank-rates-api/)
- [ERP integration guides](https://allratestoday.com/docs/integrations/)
- [API documentation](https://allratestoday.com/docs/#central-bank) · [Interactive reference](https://allratestoday.com/api-reference/) · [Methodology](https://allratestoday.com/official-rates-methodology/)
- [Register](https://allratestoday.com/register) · [Dashboard](https://allratestoday.com/profile) · [Pricing](https://allratestoday.com/pricing/)
- [Status](https://allratestoday.com/status)
- [Postman collection](https://allratestoday.com/postman/)
- [GitHub](https://github.com/AllRates-Today/imf-exchange-rate)

## 📜 License

MIT
