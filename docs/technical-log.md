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

### 2026-03-31 — Project Management — First meeting with advisor + meeting notes

**What was done:**
First online meeting with Prof. Josiane Porto (30 min). Progress approved. Key decisions recorded in `docs/meetings/ata_2026-03-31.md`.

**Decisions confirmed:**
- Model with 2010–2024 first; add 2000–2009 only if beneficial after initial results
- HS2 sectoral classification kept as-is (OECD technology intensity)
- 34 variables and monthly window validated — cleared to proceed to modeling
- EDA charts → appendix (with mention in body text)
- Variable glossary → appendix of TCC
- TCC2 limit: 30 pages; ~50% results/discussion, ~50% theory/methodology
- GitHub: not mandatory, but well received if public
- Technical documentation is mandatory (most relevant code parts)

**Result:**
- `docs/meetings/ata_2026-03-31.md` — full meeting notes

**Next step:**
Begin Modeling phase (`04_modeling.ipynb`).

---

### 2026-04-29 — Modeling — Notebook setup and helper functions

**What was done:**
Started `04_modeling.ipynb`. Created full folder structure for models and results. Defined all helper functions shared across the three algorithms.

**Technical details:**
- Folder structure: `models/{arima,random_forest,lightgbm}/`, `results/{forecasts,figures}/`
- Section 1 (Setup): imports, path constants, PARTNERS dict, TARGETS list (8 per country)
- Section 2 (Load Data): loaded dataset_can/mex/bra.csv; validated shape (180×97), date range (2010-01 to 2024-12), zero NaN in target columns
- Section 3 (Features & Helpers):
  - `get_feature_cols(df)`: returns 73 feature columns (excludes all exports/imports columns)
  - `get_train_test_ml(df, target)`: returns X_train (132×73), X_test (36×73), y_train, y_test on log scale; drops NaN rows from lag12
  - `get_train_test_arima(df, target)`: returns diff_log train (143,) and test (36,) series
  - `compute_metrics(y_true, y_pred, iso, target, algorithm)`: returns MAE, RMSE, MAPE dict

**Split confirmed:**
- ML (RF, LightGBM): 132 train / 36 test (144 - 12 NaN from lag12)
- ARIMA: 143 train / 36 test (144 - 1 NaN from first difference)

**Result:** `notebooks/04_modeling.ipynb` — Sections 1–3 complete and executed

**Next step:** Section 4 — Train 24 ARIMA models

---

### 2026-04-30 — Modeling — 72 models trained (ARIMA, Random Forest, LightGBM)

**What was done:**
Completed `04_modeling.ipynb` Sections 4–7. Trained all 72 models across 3 algorithms, 3 countries, and 8 target series. Generated metrics, forecast plots, and final ranking.

**Technical details:**

**Section 4 — ARIMA (24 models):**
- Strategy: walk-forward forecast on `diff_log_{target}` series (stationary)
- Order selection: AIC grid search over p∈{0–3}, d∈{0–1}, q∈{0–3} on training set
- Forecast: 1-step-ahead re-fit for each of the 36 test months
- Reconstruction: diff_log predictions accumulated back to log scale
- Dominant order: (3,0,3) across most series
- Results saved: `models/arima/arima_{iso}_{target}.pkl`, `results/forecasts/arima_metrics.csv`

**Section 5 — Random Forest (24 models):**
- Strategy: direct multi-step forecast using 73 features on log scale
- Hyperparameter tuning: Optuna, 30 trials, TimeSeriesSplit(5 folds)
- Search space: n_estimators [100–600], max_depth [3–20], min_samples_leaf [1–20], max_features [0.3–1.0]
- Results saved: `models/random_forest/rf_{iso}_{target}.pkl`, `results/forecasts/rf_metrics.csv`

**Section 6 — LightGBM (24 models):**
- Same structure as RF; search space includes learning_rate, num_leaves, subsample, colsample_bytree, reg_alpha, reg_lambda
- Results saved: `models/lightgbm/lgbm_{iso}_{target}.pkl`, `results/forecasts/lgbm_metrics.csv`

**Section 7 — Results comparison:**
- Combined metrics table: `results/forecasts/metrics_all.csv` (72 rows)
- MAPE bar chart: `results/figures/mape_comparison.png`
- 24 actual vs predicted plots: `results/figures/forecasts/{iso}_{target}.png`

**Final metrics (average across 24 series):**

| Algorithm | MAE | RMSE | MAPE |
|-----------|-----|------|------|
| Random Forest | 0.2114 | 0.2347 | 0.94% |
| LightGBM | 0.2243 | 0.2460 | 0.99% |
| ARIMA | 0.3185 | 0.3541 | 1.41% |

**Wins per series (best MAPE):** ARIMA 9/24, LightGBM 8/24, Random Forest 7/24

**Decision made:** All metrics on log scale. ARIMA competitive on individual series despite lower aggregate ranking — relevant for thesis discussion.

**Result:** `04_modeling.ipynb` complete and executed end-to-end. All 72 `.pkl` files saved.

**Next step:** Thesis writing — Results and Discussion section

---

### 2026-04-30 — Evaluation — Phase 5 notebook skeleton created (05_evaluation.ipynb)

**What was done:**
Created `notebooks/05_evaluation.ipynb` as a structured skeleton for the CRISP-DM Evaluation phase. The notebook is designed to surface the exchange rate–trade flow relationship through three complementary analytical lenses: feature importance, SHAP values, and ARIMA vs ML MAPE gap.

**Motivation:**
The 72 trained models predict trade volumes but do not directly expose how exchange rate variables influence forecasts. Three analysis paths were defined to answer the thesis research question:
1. **Feature importance** (RF + LightGBM): ranks FX and REER columns among all 73 features
2. **SHAP values**: quantifies direction and magnitude of each FX variable's per-prediction contribution
3. **ARIMA vs ML MAPE gap**: ARIMA has no FX features; the gap `MAPE_ARIMA - MAPE_best_ML` measures predictive value added by exchange rate information

**Technical details:**
- Section 1 — Setup: imports (`shap`, `pickle`, `matplotlib`, `pandas`, `numpy`); defines `FX_COLS` (USD/CAD, USD/MXN, USD/BRL + lag/MA variants), `REER_COLS`, path constants
- Section 2 — Load artifacts: loads 48 ML `.pkl` models (24 RF + 24 LightGBM from `models/`); loads `results/forecasts/metrics_all.csv`
- Section 3 — Feature importance: aggregates `feature_importances_` across all 24 models per algorithm; generates bar chart with FX/REER columns highlighted in red
- Section 4 — SHAP analysis: `shap.TreeExplainer(model)` on test set for all 24 RF models; `shap.summary_plot` across all series
- Section 5 — SHAP dependence plots: bilateral FX vs trade for 6 combinations (exports/imports total × 3 countries); shows direction of exchange rate effect
- Section 6 — ARIMA vs ML gap: merges ARIMA and best-ML MAPE by series; calculates `gap_pp = MAPE_ARIMA - MAPE_best_ML`; positive = ML adds value; bar chart by sector
- Section 7 — FX sensitivity heatmap: mean |SHAP| of all FX+REER features per (iso, target) combination; 24-cell heatmap
- Section 8 — Thesis summary: consolidated table for Results and Discussion section

**Prerequisite:** `pip install shap` required before running Sections 4–7.

**Decision made:**
The SHAP dependence plots and ARIMA/ML gap are the most thesis-relevant outputs — they directly answer "how do exchange rate fluctuations affect trade flows?" The feature importance chart is supporting evidence.

**Result:** `notebooks/05_evaluation.ipynb` — skeleton complete, 8 sections, not yet executed.

**Next step:** Install shap and run 05_evaluation.ipynb end-to-end.

---

### 2026-05-01 — Evaluation — Statistical tests, Naïve baseline and Ljung-Box added to 05_evaluation.ipynb

**What was done:**
Extended `notebooks/05_evaluation.ipynb` with two new sections (Naïve Baseline and ARIMA Residual Diagnostics) and documented the forecasting horizon scope. Notebook restructured from 9 to 10 sections, 27 to 32 cells.

**Additions:**

**Section 3 — Naïve Baseline (new):**
- Implements a random-walk baseline: `log_trade[t] = log_trade[t-1]` (last observed value)
- Applied to the same 36-month test window as all other models
- Purpose: establishes the minimum acceptable performance bar; prevents the "is your model better than trivial?" objection
- Computes MAE, RMSE, MAPE; appended to `df_all_ext` for side-by-side comparison

**Section 4.1 — ARIMA Residual Diagnostics — Ljung-Box (new):**
- Re-fits each of the 24 ARIMA models on the full training set (one fit per series, ~5 seconds total)
- Runs `statsmodels.stats.diagnostic.acorr_ljungbox` at lag=10
- H₀: residuals are white noise (model adequately specified)
- Generates ACF plots for exports_total per country: `results/figures/evaluation/arima_residuals_acf.png`
- Validates ARIMA adequacy before statistical comparison against ML models

**Forecasting horizon scope (documented in Section 3 markdown):**
- ARIMA: 1-step-ahead walk-forward — re-trains at each of the 36 test months; strictly simulates real-world sequential use
- RF / LightGBM: 36-step direct — trained once on 2010–2021; predicts all 36 test months in a single call using known feature values
- Asymmetric protocols are intentional and documented as methodological scope: ARIMA operates under stricter conditions; ML compensates with 73 features

**Revised notebook structure (10 sections):**
1. Setup
2. Load Data and Models
3. Naïve Baseline ← new
4. Statistical Significance Tests
   - 4.1 Ljung-Box (ARIMA residuals) ← new
   - 4.2 Friedman
   - 4.3 Wilcoxon + Bonferroni
   - 4.4 Diebold-Mariano
5. Feature Importance
6. SHAP Analysis
7. Exchange Rate Focus
8. ARIMA vs ML Gap
9. Sector Sensitivity
10. Summary for Thesis

**Decision made:**
Naïve baseline chosen over other simple models (moving average, seasonal naïve) because the series exhibit strong momentum — random walk is both the most common benchmark in trade forecasting literature and the hardest to beat for monthly macro data.

**Result:** `notebooks/05_evaluation.ipynb` — 32 cells, ready to execute (requires `pip install shap` first).

**Next step:** Execute 05_evaluation.ipynb end-to-end and write Results and Discussion section.

---

### [2026-05-01] — Evaluation — 05_evaluation.ipynb: full review + LightGBM SHAP + bug fixes

**What was done:**
Full review of `05_evaluation.ipynb` after end-to-end execution. Notebook expanded from 32 to 36 cells — SHAP analysis now covers both RF and LightGBM. Three bugs identified and corrected.

**Key execution results (confirmed from cell outputs):**

*Naïve Baseline (Section 3):*
- Naïve MAPE: 0.32% — beats all three models (RF 0.94%, LightGBM 1.03%, ARIMA 1.41%)
- Expected: Naïve has structural information advantage (observes actual t-1 at each step); ML models predict all 36 months without test-period feedback
- Documented asymmetry in notebook markdown and diary; framed as methodological scope for thesis

*Ljung-Box — ARIMA residuals (Section 4.1):*
- 22/24 series: p >= 0.05 → white noise → ARIMA well-specified
- 2 problematic: CAN exports_commodities (p=0.0006), CAN imports_total (p=0.0146)
- Residual autocorrelation in Canada commodity series may reflect oil price shock dynamics not captured by selected ARIMA order

*Statistical significance (Sections 4.2–4.4):*
- Friedman: chi2=1.75, p=0.417 — not significant; low power with n=24
- Wilcoxon+Bonferroni: all three pairs are ties (Bonferroni-adjusted p > 0.05)
- Diebold-Mariano: RF > LightGBM in 83% of series; RF > ARIMA in 75%; LightGBM > ARIMA in 83%
- DM is more powerful: uses 36-step error sequences (864 obs) vs 24 MAPE summaries in Wilcoxon

*Feature importance (Section 5):*
- RF: WTI_oil #1; FX_USD_CAD_ma6 rank 13, REER_MEX_lag3 rank 14
- LightGBM: FX_USD_CAD_pct #3, FX_USD_MXN_pct #4 — FX changes appear more prominently
- Interpretation: LightGBM captures FX shocks (pct); RF captures medium-term trends (ma6)

*ARIMA vs ML gap (Section 8):*
- Mean gap: +0.54 pp favouring ML (10.9% average MAPE reduction)
- Largest gains: CAN imports_high-tech (2.48 pp, 71%), BRA exports_commodities (2.04 pp, 70%)
- 8/24 series: ARIMA outperforms ML — documented, not treated as failure

**SHAP LightGBM — 5 new cells:**

| Cell | Content |
|------|---------|
| 21 | TreeExplainer on all 24 LightGBM models; stores shap_lgbm_values, shap_lgbm_X_test |
| 23 | SHAP summary plot — LightGBM (all 24 models, 864 test observations) |
| 24 | Side-by-side RF vs LightGBM mean|SHAP| per feature (top 15, FX/REER highlighted red) |
| 27 | SHAP FX dependence plots — LightGBM (exports_total and imports_total) |
| 32 | Sector sensitivity updated: average of RF + LightGBM sensitivity scores per series |

**Bug fixes:**
1. fx_reer_cols (Cell 18) was used in Cell 28 — hidden dependency removed: Cell 28 now uses inline `any(fx in c for fx in FX_COLS + REER_COLS)`; FX_COLS/REER_COLS in Setup (Cell 2)
2. Heatmap title (Cell 33): "Random Forest" → "RF + LightGBM average"
3. Naive Baseline markdown (Cell 5): added paragraph on information asymmetry between Naive and ML forecasters

**Decision:**
SHAP extended to LightGBM because: (a) TreeExplainer supports LightGBM natively; (b) RF and LightGBM weight different FX representations (RF: ma6; LightGBM: pct) — cross-model agreement strengthens thesis; (c) RF+LightGBM average for sector sensitivity is more robust than single-model attribution.

**Result:**
- notebooks/05_evaluation.ipynb — 36 cells
- Figures from previous execution confirmed: arima_residuals_acf.png, feature_importance_top15.png, shap_rf_summary.png, shap_fx_dependence.png, arima_ml_gap_by_sector.png, fx_sensitivity_heatmap.png
- Pending re-execution (Section 6+): shap_lgbm_summary.png, shap_comparison_rf_lgbm.png, shap_lgbm_fx_dependence.png

**Next step:** Re-run notebook from Cell 19 (Section 6) to generate LightGBM SHAP figures. Then write Results and Discussion section of thesis.

---

### [2026-05-04] — Evaluation — 05_evaluation.ipynb re-executed end-to-end (36 cells)

**What was done:**
Full re-execution of `05_evaluation.ipynb` after LightGBM SHAP expansion. All 36 cells ran without errors.

**New figures confirmed generated:**
- `results/figures/evaluation/shap_lgbm_summary.png` — LightGBM SHAP summary plot (864 observations × 73 features)
- `results/figures/evaluation/shap_comparison_rf_lgbm.png` — RF vs LightGBM mean|SHAP| side-by-side (top 15 features, FX/REER highlighted)
- `results/figures/evaluation/shap_lgbm_fx_dependence.png` — bilateral FX vs LightGBM SHAP contribution for exports/imports_total

**Result:** Phase 5 (Evaluation) complete. All statistical tests, interpretability analyses, and thesis figures generated and verified.

**Next step:** Write Results and Discussion section of TCC2.

---

### [2026-05-04] — Writing — Master writing plan created + data validation

**What was done:**
Pre-writing session: all reference documents in `/docs` read, actual performance metrics validated from CSV, master writing plan document created.

**Technical details:**
- Read: `docs/Plagio.md`, `docs/tcc1/Modelo_Latex_TCC_II.md`, `docs/meetings/ata_2026-03-31.md`
- Attempted to read 5 PDF papers in `docs/references/papers/` — failed (pdftoppm not installed on Windows). Citations available from TCC1 text. Poppler installation instructions provided.
- Validated all model metrics from `results/forecasts/metrics_all.csv` (72 models):
  - MAPE averages: RF 0.9381%, LightGBM 1.0276%, ARIMA 1.4143%
  - Wins (lowest MAPE per series): RF 9/24, ARIMA 8/24, LightGBM 7/24
  - Corrected prior sync entry (30/04) which had wins inverted: was "ARIMA 9, RF 7" — correct is "RF 9, ARIMA 8"
- Created `tcc/tcc2-writing-plan.md` — master reference for all writing:
  - 10 inviolable rules (ABNT, citation preservation, page limit, SI emphasis)
  - Full structure with per-section page estimates
  - Section-by-section writing guide with TCC1 reuse map
  - Actual performance data (country-level and sector-level breakdowns)
  - Citation list per section
  - Writing order recommendation: Results → Discussion → Methodology → Theory → Introduction → Conclusion → Abstract
- Fixed checklist: Phase 5 marked complete; corrected wins and LightGBM MAPE
- Added trigger in CLAUDE.md: always read `tcc/tcc2-writing-plan.md` before any writing task

**Decision made:**
No retraining of models needed. Evaluation findings are legitimate results, not process errors. 2 problematic ARIMA series (CAN exports_commodities, CAN imports_total — Ljung-Box fail) documented as limitation. Naïve baseline advantage explained as structural information asymmetry, not model failure.

Deployment section = written text only (no live demo, no pipeline execution needed). Interviews with experts = still pending, must happen before 31/05/2026.

**Result:** `tcc/tcc2-writing-plan.md` created. Checklist and sync updated. Ready to begin writing Results section.

**Next step:** Write Section 6 — Results (start with descriptive overview, then model comparison table, then statistical tests, then SHAP).

---

*End of log. New entries will appear above this line.*
