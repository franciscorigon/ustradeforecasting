# Technical Log — TCC2 Trade Flow Forecasting

> Detailed documentation of every technical step taken in this project.
> Entries are ordered chronologically (oldest first).
> This log feeds directly into the Methodology and Results sections of the thesis.

---

## Log Entries

---

### 2026-03-26 — Setup — Project repository initialized

**What was done:**
Initial project repository created with full directory structure. Core tracking documents and notebooks established.

**Technical details:**
- Repository: `c:/Projects/trade-forecasting/`
- Directory structure: `data/raw/`, `data/processed/`, `notebooks/`, `src/`, `models/`, `reports/figures/`, `docs/`
- Notebook ready for data collection: `notebooks/01_data_collection.ipynb`
- Python environment: 3.10+

**Decision made:**
Adopted CRISP-DM as the project methodology, as defined in TCC1. Models to compare: ARIMA, Random Forest, LightGBM.

**Result:**
Full project scaffold in place.

**Next step:**
Obtain FRED API key and run `notebooks/01_data_collection.ipynb`.

---

### 2026-03-26 — Data Understanding — FRED data collection completed

**What was done:**
Collected all macroeconomic and exchange rate series from FRED API covering 2000-01-03 to 2026-03-20. Three CSV files saved to `data/raw/`.

**Technical details:**
- API: FRED (Federal Reserve Economic Data), authenticated via `FRED_API_KEY`
- Period: 2000-01-01 to 2026-03-20 (present)

**Exchange Rates (daily):**

| Series | Description | Observations | Missing |
|--------|-------------|-------------|---------|
| DEXCAUS | USD/CAD | 6,840 | 268 (weekends/holidays) |
| DEXMXUS | USD/MXN | 6,840 | 268 (weekends/holidays) |
| DEXBZUS | USD/BRL | 6,840 | 268 (weekends/holidays) |

**Macro Indicators (monthly, unless noted):**

| Series | Description | Observations | Missing |
|--------|-------------|-------------|---------|
| FEDFUNDS | Fed Funds Rate | 314 | 0 |
| CPIAUCSL | CPI (inflation) | 314 | 1 |
| GDPC1 | Real GDP (quarterly) | 104 | 0 |
| UNRATE | Unemployment Rate | 314 | 1 |
| INDPRO | Industrial Production Index | 314 | 0 |

**Trade Indices (daily):**

| Series | Description | Observations | Missing |
|--------|-------------|-------------|---------|
| DTWEXBGS | Trade Weighted USD Index | 5,275 | 208 |

**Decision made:**
Missing values in exchange rates (268 obs) are expected — FRED reports business days only; weekends and US federal holidays are NaN. Treatment (forward-fill or resample to monthly) will happen in Data Preparation phase. The single missing values in CPIAUCSL and UNRATE will be inspected during EDA.

**Result:**
- `data/raw/fred_exchange_rates.csv` — shape (6840, 3)
- `data/raw/fred_macro.csv` — shape (314, 5)
- `data/raw/fred_trade_indices.csv` — shape (5275, 1)

**Next step:**
Collect bilateral trade flow data from UN Comtrade (exports/imports by sector for US↔Canada, Mexico, Brazil). Then run EDA notebook (`02_eda.ipynb`).

---

### 2026-03-26 — Data Understanding — FRED partner countries macro + REER collected

**What was done:**
Collected GDP, CPI, interest rates and REER for Canada, Mexico, Brazil and USA from FRED. Zero missing values across all 13 series.

**Technical details:**

| Series ID | Description | Country | Freq | Obs | Missing |
|-----------|-------------|---------|------|-----|---------|
| CANGDPNQDSMEI | Real GDP | Canada | Quarterly | 95 | 0 |
| CANCPIALLMINMEI | CPI | Canada | Monthly | 303 | 0 |
| IRSTCB01CAM156N | Central Bank Rate | Canada | Monthly | 288 | 0 |
| MEXGDPNQDSMEI | Real GDP | Mexico | Quarterly | 95 | 0 |
| MEXCPIALLMINMEI | CPI | Mexico | Monthly | 295 | 0 |
| IR3TIB01MXM156N | 3M Interbank Rate | Mexico | Monthly | 314 | 0 |
| NGDPRSAXDCBRQ | Real GDP | Brazil | Quarterly | 104 | 0 |
| BRACPIALLMINMEI | CPI | Brazil | Monthly | 304 | 0 |
| IRSTCB01BRM156N | Central Bank Rate | Brazil | Monthly | 288 | 0 |
| RBUSBIS | REER | USA | Monthly | 314 | 0 |
| RBCABIS | REER | Canada | Monthly | 314 | 0 |
| RBMXBIS | REER | Mexico | Monthly | 314 | 0 |
| RBBRBIS | REER | Brazil | Monthly | 314 | 0 |

**Decision made:**
Mexico's OECD central bank rate series (`IRSTCB01MXM156N`) does not exist in FRED. Used `IR3TIB01MXM156N` (3-month interbank rate) as substitute — standard proxy for monetary policy stance in empirical trade literature. Canada and Brazil use official central bank overnight rates.

REER sourced from BIS (Bank for International Settlements) via FRED — preferred over IMF REER due to complete monthly coverage from 2000 to 2026.

**Result:**
- `data/raw/fred_gdp_partners.csv` — (104, 3): GDP_CAN, GDP_MEX, GDP_BRA — 2000Q1 to 2025Q3
- `data/raw/fred_cpi_partners.csv` — (304, 3): CPI_CAN, CPI_MEX, CPI_BRA — 2000-01 to 2025-04
- `data/raw/fred_rates_partners.csv` — (314, 3): RATE_CAN, RATE_MEX_3M, RATE_BRA — 2000-01 to 2026-02
- `data/raw/fred_reer.csv` — (314, 4): REER_USA, REER_CAN, REER_MEX, REER_BRA — 2000-01 to 2026-02

**Next step:**
Collect bilateral trade flow data from UN Comtrade (US exports/imports by HS sector with Canada, Mexico, Brazil).

---

### 2026-03-26 — Data Understanding — UN Comtrade bilateral trade flows collected

**What was done:**
Collected bilateral trade flow data (USA ↔ Canada, Mexico, Brazil) from UN Comtrade Plus API. Three files saved: annual totals, annual by HS2 sector, and monthly totals. Discovered that monthly data is only available from 2010 onward in the Comtrade Plus API.

**Technical details:**
- API: UN Comtrade Plus (`comtradeapicall` Python library)
- Reporter: USA (code 842)
- Partners: Canada (124), Mexico (484), Brazil (76)
- Flow: Export (X) + Import (M)
- Classification: HS (Harmonized System)

**Annual total trade (2000–2024):**
- 150 rows — 25 years × 3 partners × 2 flows
- File: `data/raw/comtrade_annual_total.csv` — shape (150, 47)

**Annual trade by HS2 sector (2000–2024):**
- 14,513 rows — 97 HS2 sectors × 3 partners × 2 flows × 25 years
- File: `data/raw/comtrade_annual_hs2.csv` — shape (14513, 47)

**Monthly total trade:**
- 1,080 rows — 15 years (2010–2024) × 12 months × 3 partners × 2 flows
- File: `data/raw/comtrade_monthly_total.csv` — shape (1080, 47)
- Period: 201001 to 202412

**Decision made:**
Monthly data for 2000–2009 is **not available** in UN Comtrade Plus API — confirmed after multiple retry attempts per year. This is a platform limitation.

**Adopted strategy (Dual Frequency Approach):**
- **Annual 2000–2024** for historical trend analysis, sectoral decomposition, and long-term elasticity discussions
- **Monthly 2010–2024** for time series forecasting models (ARIMA, Random Forest, LightGBM)
- Rationale: 15-year monthly window (2010–2024) captures post-GFC recovery, European debt crisis, commodity supercycle decline, and COVID-19 pandemic shock — sufficient structural variation for robust model estimation.

This dual-frequency approach will be explicitly justified in the thesis Methodology section (section 3.2).

**Result:**
- `data/raw/comtrade_annual_total.csv` — (150, 47)
- `data/raw/comtrade_annual_hs2.csv` — (14513, 47)
- `data/raw/comtrade_monthly_total.csv` — (1080, 47), 2010–2024 only

**Next step:**
Run EDA (`02_eda.ipynb`).

---

### 2026-03-26 — Data Understanding — Exploratory Data Analysis completed

**What was done:**
Comprehensive EDA of all collected raw data covering exchange rates, macroeconomic indicators, trade indices, and bilateral trade flows. Generated publication-quality visualizations examining temporal patterns, volatility, and sectoral composition.

**Technical details:**
- Notebook: `notebooks/02_eda.ipynb`
- Visualizations generated (PNG, 150 DPI, saved to `reports/figures/`):
  1. Exchange rate time series (USD/CAD, MXN, BRL) — daily 2000–2026
  2. REER comparison (USA, Canada, Mexico, Brazil) — BIS monthly 2000–2026
  3. Trade flows annual totals (USA bilateral) — 2000–2024
  4. Trade flows monthly (2010–2024 window)
  5. Top 10 HS2 sectors by trade volume (2024)

**Key findings:**
- Exchange rates show USD appreciation during crisis periods (2008, 2020)
- REER demonstrates divergence: USD strength 2014–2019, weakening 2020–2026
- Bilateral trade contracted sharply in 2008–2009; COVID-19 disruption visible Mar–Apr 2020
- Top sectors: machinery (HS 84), electrical (85), vehicles (87), minerals (27)
- Daily FX data shows expected business-day gaps; macro data quality excellent

**Decision made:**
Confirmed dual-frequency modeling approach: annual 2000–2024 for sectoral elasticity; monthly 2010–2024 for forecasting models.

**Result:**
- `notebooks/02_eda.ipynb` — executed, all cells passed
- Figures `01_` through `05_` saved to `reports/figures/`

**Next step:**
Collect World Bank indicators, then expand EDA.

---

### 2026-03-27 — Data Understanding — World Bank indicators collected

**What was done:**
Collected 6 macroeconomic indicators from the World Bank API for all 4 countries (USA, Canada, Mexico, Brazil), annual frequency, 2000–2025.

**Technical details:**
- API: World Bank via `wbdata` Python library
- Countries: USA, CAN, MEX, BRA
- Frequency: Annual
- Period: 2000–2025 (26 years × 4 countries = 104 rows)

| Indicator ID | Description | Missing |
|---|---|---|
| NY.GDP.PCAP.CD | GDP per capita (current USD) | 4 (2025, not yet published) |
| NY.GDP.MKTP.CD | GDP total (current USD) | 4 (2025) |
| SP.POP.TOTL | Population total | 4 (2025) |
| FP.CPI.TOTL.ZG | Inflation, CPI (annual %) | 4 (2025) |
| BN.CAB.XOKA.CD | Current account balance (USD) | 4 (2025) |
| BX.KLT.DINV.CD.WD | FDI inflows (USD) | 4 (2025) |

**Decision made:**
Missing values (4 per indicator) are all from 2025 — World Bank publishes with ~6-month lag. These will be excluded or imputed in Data Preparation. All 2000–2024 data is complete.

**Result:**
- `data/raw/worldbank_indicators.csv` — shape (104, 8)

**Next step:**
Expand EDA with World Bank visualizations and additional data sources.

---

### 2026-03-27 — Data Understanding — Industrial production and commodities collected + EDA expanded + HS2 sectoral classification

**What was done:**
Collected industrial production indices for Canada, Mexico and Brazil, and prices for three key commodities (WTI crude oil, soybean, iron ore). Expanded the EDA notebook with six new sections and implemented a sectoral HS2 classification aligned with TCC1 Section 2.3.2.

**Technical details — new FRED series:**

| Series ID | Description | Obs | Frequency |
|-----------|-------------|-----|-----------|
| CANPROINDMISMEI | Industrial Production — Canada | 290 | Monthly |
| MEXPRINTO02IXOBSAM | Industrial Production — Mexico | 286 | Monthly |
| BRAPROINDMISMEI | Industrial Production — Brazil | 291 | Monthly |
| DCOILWTICO | WTI Crude Oil Price | 6,841 | Daily |
| PSOYBUSDM | Soybean Price | 314 | Monthly |
| PIORECRUSDM | Iron Ore Price | 314 | Monthly |

Note: Mexico's standard OECD series (`MEXPROINDMISMEI`) does not exist in FRED. Used `MEXPRINTO02IXOBSAM` (Total Industry Including Construction, index 2015=100) as equivalent.

**EDA expansion (`02_eda.ipynb`, 28 → 37 cells):**
- Section 12: Industrial Production Index — Canada, Mexico, Brazil (2000–2024)
- Section 13: Commodity prices — WTI oil, soybean, iron ore (2000–2026)
- Section 14: HS2 sectoral classification into 3 groups + High-Tech trade by partner country

**HS2 classification (OECD technology intensity, aligned with TCC1 Section 2.3.2):**

| Category | HS2 Chapters | Rationale |
|----------|-------------|-----------|
| Commodities | 1–15, 25–27, 41–47, 71–72 | Agricultural, mineral, energy raw materials |
| High-Tech | 30, 84, 85, 88, 90, 91 | Pharma, machinery/computers, electronics, aircraft, instruments |
| Manufactured Goods | All remaining chapters | Chemicals, plastics, textiles, vehicles, metals |

**Key findings — sectoral analysis:**
- High-Tech (HS 84, 85, 88, 90) represents a significant share of US trade with Canada and Mexico, confirming the importance of sectoral disaggregation as proposed in TCC1
- Canada leads in High-Tech trade volume; Mexico growing since 2010 (USMCA effect)
- Brazil's High-Tech trade with the US is substantially lower, consistent with its commodity-export profile

**Result:**
- `data/raw/fred_indpro_partners.csv` — (291, 3)
- `data/raw/fred_commodities.csv` — (6841, 3) daily + monthly
- `notebooks/02_eda.ipynb` — 37 cells, all outputs saved
- `reports/figures/10_indpro_partners.png`
- `reports/figures/11_commodities.png`
- `reports/figures/12_sector_categories.png`
- `reports/figures/13_hightech_by_partner.png`

**Decision made:**
Phase 2 (Data Understanding) considered complete. All data sources defined in TCC1 Section 3.2.1 have been collected and explored.

**Next step:**
Begin Data Preparation phase (`03_data_preparation.ipynb`): frequency alignment to monthly, outlier treatment, feature engineering, construction of final modeling dataset.

---

*End of log. New entries will appear above this line.*
