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
Collected industrial production indices for Canada, Mexico and Brazil, and prices for three key commodities (WTI crude oil, soybean, iron ore). Expanded the EDA notebook with six new sections and implemented a sectoral HS2 classification (Commodities, Manufactured Goods, and High-Tech) aligned with TCC1 Section 2.3.2, with emphasis on high-technology sectors (HS 30, 84, 85, 88, 90, 91) as identified as critical for trade asymmetry analysis.

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

Sectoral modeling strategy defined: ARIMA, Random Forest and LightGBM will be trained independently for each of the three HS2 categories (Commodities, Manufactured Goods, High-Tech), per country pair. This allows direct comparison of exchange rate sensitivity across sector types — e.g., whether a BRL depreciation affects US high-tech exports differently than commodity exports. This approach is aligned with TCC1 objective of identifying the sectors most vulnerable to exchange rate fluctuations.

Note: High-Tech trade volume with Brazil is substantially lower than with Canada and Mexico — monthly data sparsity for this segment will be assessed during Data Preparation before modeling.

**Next step:**
Begin Data Preparation phase (`03_data_preparation.ipynb`): frequency alignment to monthly, outlier treatment, feature engineering, construction of final modeling dataset.

---

### 2026-03-27 — Data Preparation — Variable selection confirmed and preparation pipeline built

**What was done:**
Confirmed the final list of independent variables for the modeling phase and built the complete Data Preparation pipeline in `notebooks/03_data_preparation.ipynb`.

**Variable selection decisions:**

| Group | Variables Included | Count |
|---|---|---|
| Bilateral exchange rate | FX_USD_CAD, FX_USD_MXN, FX_USD_BRL | 3 |
| REER | REER_USA, REER_CAN, REER_MEX, REER_BRA | 4 |
| US Macro | FEDFUNDS, CPI_USA, GDP_USA, UNRATE_USA, INDPRO_USA | 5 |
| Partner interest rates | RATE_CAN, RATE_MEX_3M, RATE_BRA | 3 |
| Partner CPI | CPI_CAN, CPI_MEX, CPI_BRA | 3 |
| Partner GDP | GDP_CAN, GDP_MEX, GDP_BRA | 3 |
| Partner industrial production | INDPRO_CAN, INDPRO_MEX, INDPRO_BRA | 3 |
| Commodities | WTI_oil, Soybean, Iron_ore | 3 |

**Variables excluded:**
- `DTWEXBGS` (Trade Weighted USD Index): excluded due to high multicollinearity with REER_USA — both measure USD strength against a basket of currencies.
- All World Bank indicators: annual frequency makes interpolation to monthly artificial; equivalent coverage already provided by monthly/quarterly FRED series (GDP, CPI, interest rates).

**Frequency alignment decisions:**
- Daily (FX rates, WTI): monthly mean via `resample('MS').mean()`
- Quarterly (GDP USA and partners): forward-fill to monthly via `resample('MS').first().ffill()` — avoids look-ahead bias from interpolation
- Monthly series: aligned to Month Start index

**Target variable approach:**
Monthly HS2 sectoral trade data is not available from UN Comtrade Plus (only aggregate monthly). Sectoral monthly estimates computed via annual HS2 proportion method: sector_share (from annual HS2 data) applied to monthly total flows within each year. Documented as methodological limitation.

**Train/test split:**
- Training: 2010-01 to 2021-12 (144 months, 80%)
- Test: 2022-01 to 2024-12 (36 months, 20%)
- Chronological split — no shuffling (time series requirement)

**Crisis dummies created:**
- `dummy_gfc`: 1 for Sep 2008–Jun 2009 (Global Financial Crisis)
- `dummy_covid`: 1 for Mar 2020–Jun 2021 (COVID-19 disruption)

**Feature engineering applied:**
- Lags: t-1, t-3, t-6, t-12 for bilateral FX rates and REER
- Moving averages: 3m, 6m, 12m for bilateral FX rates
- Percentage changes (MoM) for bilateral FX rates and REER_USA
- Calendar features: month, quarter, year

**Transformations:**
- Log (log1p) applied to all trade flow columns
- First differences of log-trade flows for ARIMA stationarity
- ADF tests run on raw and differenced series
- Min-max scaling deferred to Modeling phase (fit only on training set)

**Result:**
- `notebooks/03_data_preparation.ipynb` — complete pipeline, 10 sections
- `data/processed/dataset_can.csv`
- `data/processed/dataset_mex.csv`
- `data/processed/dataset_bra.csv`
- `data/processed/dataset_combined.csv`
- `reports/figures/14_fx_with_crises.png`
- `reports/figures/15_correlation_matrix.png`

**Next step:**
Begin Modeling phase (`04_modeling.ipynb`): ARIMA, Random Forest, LightGBM — trained separately per country pair × sector × direction.

---

### 2026-03-27 — Data Preparation — Variable catalogue created

**What was done:**
Created a complete variable catalogue documenting all raw and processed variables in the project. New folder `data/codebook/` created to house project reference documents.

**Technical details:**
- File: `data/codebook/variable-catalogue.md`
- Sections: Raw Data (FRED, UN Comtrade, World Bank) + Processed Data (all 97 columns documented)
- Each variable documented with: source symbol, full name, frequency, period, transformation applied
- Excluded variables explicitly documented with justification
- Train/test split documented (2010–2021 train / 2022–2024 test)

**Result:**
- `data/codebook/variable-catalogue.md` — complete variable reference for the project

**Next step:**
Begin Modeling phase (`04_modeling.ipynb`).

---

### 2026-03-29 — Documentation — Plain-language variable glossary created

**What was done:**
Created `data/codebook/glossary.md` — a plain-language reference for all project variables, intended for readers without economics or data science background.

**Technical details:**
- All base variables documented alphabetically with full name and plain-language explanation
- Derived feature patterns explained generically (lag, MA, pct, log, diff_log) to avoid 57 near-identical rows
- Excluded variables listed with justification
- Complements `variable-catalogue.md` (technical) with an accessible reference

**Result:**
- `data/codebook/glossary.md`

**Next step:**
Begin Modeling phase (`04_modeling.ipynb`).

---

### 2026-03-29 — Documentation — Diário de Bordo reorganized + checklist expanded

**What was done:**
- Rewrote `docs/diario-de-bordo.md` from scratch in correct chronological order (oldest first). Previous version had sessions out of order, steps renumbered per session, and World Bank entry misplaced after the Session 1 summary. New version: 4 sessions, 15 steps numbered continuously.
- Expanded `tcc2-checklist.md` Phase 4 (Modeling) to list all 72 individual models explicitly: 24 per algorithm (ARIMA, RF, LightGBM) × 3 countries × 2 directions × 4 sectors.

**Modeling scope clarified:**
- 24 unique target series: 3 countries × 2 directions (exports/imports) × 4 sectors (total, commodities, manufactured goods, high-tech)
- 73 input features per model run (base features + dummies + lags + MAs + %change + calendar)
- 72 total models: 24 targets × 3 algorithms
- ARIMA uses `diff_log_{target}` (stationary); RF and LightGBM use `log_{target}`
- Raw target columns are for reference and economic interpretation only

**Result:**
- `docs/diario-de-bordo.md` — fully reorganized, chronological
- `tcc2-checklist.md` — 72 model checkboxes added to Phase 4

**Next step:**
Begin Modeling phase (`04_modeling.ipynb`).

---

*End of log. New entries will appear above this line.*
