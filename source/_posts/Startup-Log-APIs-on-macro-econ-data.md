---
title: 'Startup Log: APIs on macro econ data'
date: 2025-10-18 02:03:41
math: true
excerpt: API I used to query macro econ data (free)
tags: [Startup, Resource]
categories:
  - Project Logs
  - Start Up Logs
  - Lessons Learned
---

# What's include
Our market analysis page includes the following information:
{% asset_img market_analysis.png screen shot of market analysis %}


- Alpha Vantage (economic indicators) [docs](https://www.alphavantage.co/documentation/)
  - Federal Funds Rate: GET https://www.alphavantage.co/query?function=FEDERAL_FUNDS_RATE&interval={daily|weekly|monthly}&apikey=KEY
  - Treasury Yields: GET https://www.alphavantage.co/query?function=TREASURY_YIELD&interval={daily|weekly|monthly}&maturity={3month|2year|5year|7year|10year|30year}&apikey=KEY
  - CPI (headline index series): GET https://www.alphavantage.co/query?function=CPI&interval={monthly|semiannual}&apikey=KEY
  - Inflation (annual): GET https://www.alphavantage.co/query?function=INFLATION&apikey=KEY
  - Retail Sales: GET https://www.alphavantage.co/query?function=RETAIL_SALES&apikey=KEY
  - Durables: GET https://www.alphavantage.co/query?function=DURABLES&apikey=KEY
  - Unemployment: GET https://www.alphavantage.co/query?function=UNEMPLOYMENT&apikey=KEY
  - Nonfarm Payroll: GET https://www.alphavantage.co/query?function=NONFARM_PAYROLL&apikey=KEY
  - Intraday equities (optional alt source): GET https://www.alphavantage.co/query?function=TIME_SERIES_INTRADAY&symbol=SPY&interval=5min&outputsize=compact&apikey=KEY
  - Rate-limit note: free tier ~5 rpm. In our stack these are proxied live (no long cache).

- FRED (macro time series) [docs](https://fred.stlouisfed.org/docs/api/fred/?utm_source=chatgpt.com)
  - Generic: GET https://api.stlouisfed.org/fred/series/observations?series_id=SERIES&file_type=json&api_key=KEY[&units=pc1][&sort_order=asc|desc][&limit=N]
  - Series used:
    - Headline CPI YoY: series_id=CPIAUCSL with units=pc1
    - Core CPI YoY: series_id=CPILFESL with units=pc1
    - Core PCE YoY: series_id=PCEPILFE with units=pc1
    - Real GDP QoQ: series_id=A191RL1Q225SBEA
    - Real GDP (level, from 2015): series_id=GDPC1&observation_start=2015-01-01
    - 30Y Mortgage Rate: series_id=MORTGAGE30US
    - VIX: series_id=VIXCLS
    - Initial Jobless Claims: series_id=ICSA
  - Rate-limit note: ~120 rpm. In our stack: CPI YoY polled at 1 rps (round‑robin), Mortgage and GDP full series hourly cache.

- DB.NOMICS (ISM PMI) [API](https://api.db.nomics.world/)
  - Manufacturing PMI: GET https://api.db.nomics.world/v22/series/ISM/pmi/pm?format=json&observations=true
  - Notes: we sanitize outliers outside 30–80 by replacing with 49.10; polled every 1s with in‑memory cache.

- Yahoo Finance via yfinance (S&P 500 intraday) [docs](https://github.com/ranaroussi/yfinance)
  - Symbol ^GSPC, interval 1m; refreshed every 60s server‑side; served from in‑memory cache for the dashboard price, change, intraday OHLCV.

- Caching cadence (server-side)
  - CPI YoY (CPIAUCSL, CPILFESL, PCEPILFE): 1 rps round‑robin
  - ISM PMI (DB.NOMICS): 1 rps
  - S&P 500 (^GSPC via yfinance): every 60s
  - Mortgage 30Y full series (MORTGAGE30US): hourly
  - Real GDP GDPC1 full series (from 2015): hourly
  - Other Alpha Vantage macro endpoints: proxied live (respect provider limits)