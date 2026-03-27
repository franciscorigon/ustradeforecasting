# Trade Flow Forecasting with ML

Predicting how exchange rate swings affect trade between the US and its top partners: Canada, Mexico, and Brazil.

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://python.org)

## What This Does

Compares three forecasting approaches:
- **ARIMA** — Classic time series baseline
- **Random Forest** — Ensemble ML
- **LightGBM** — Gradient boosting

Goal: Figure out which model best captures the relationship between currency fluctuations and trade volumes.

## Project Structure

```
├── CLAUDE.md                  # Claude Code configuration
├── tcc2-central-sync.md       # Progress tracking
│
├── .claude/skills/            # Claude Code skills
│   ├── python-ml-code/        # Python/ML coding
│   ├── academic-writing/      # TCC writing (ABNT)
│   ├── advisor-mentor/        # Advisor simulation
│   ├── meeting-tracker/       # Meeting notes
│   ├── git-docs-style/        # Documentation
│   └── natural-writing/       # Writing style
│
├── data/
│   ├── raw/                   # Original data from APIs
│   ├── processed/             # Clean, ready-to-use data
│   └── external/              # Third-party datasets
│
├── notebooks/                 # Analysis notebooks
├── src/                       # Reusable Python code
├── models/                    # Saved trained models
├── reports/                   # Figures and results
├── docs/                      # Meeting notes, references
└── tcc/                       # TCC text files
```

## Quick Start

### 1. Clone and setup

```bash
git clone https://github.com/YOUR_USERNAME/tcc2-trade-forecasting.git
cd tcc2-trade-forecasting

python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

### 2. Get your API key

```bash
cp .env.example .env
# Edit .env and add your FRED_API_KEY
```

Get a free key at [fred.stlouisfed.org](https://fred.stlouisfed.org/)

### 3. Run the notebooks

```bash
jupyter notebook
```

Start with `01_data_collection.ipynb` and go in order.

## Using with Claude Code

This repo is configured for Claude Code. Available commands:

| Command | What it does |
|---------|--------------|
| `/code` | Python/ML coding help |
| `/write` | Academic writing (ABNT) |
| `/advisor` | Advisor simulation |
| `/meeting` | Record meeting notes |
| `/docs` | README, commits, docs |
| `/status` | Check project progress |

## Data Sources

| Source | Data | Link |
|--------|------|------|
| FRED | Exchange rates, interest rates, CPI | [fred.stlouisfed.org](https://fred.stlouisfed.org/) |
| UN Comtrade | Bilateral trade flows | [comtradeplus.un.org](https://comtradeplus.un.org/) |
| World Bank | GDP, inflation | [data.worldbank.org](https://data.worldbank.org/) |

## Tech Stack

- **Data:** pandas, numpy, sqlalchemy
- **Time Series:** statsmodels (ARIMA)
- **ML:** scikit-learn, lightgbm, optuna
- **Explainability:** shap
- **Viz:** matplotlib, seaborn, plotly

## Results

*Coming soon — project in progress.*

| Model | RMSE | MAE | MAPE |
|-------|------|-----|------|
| ARIMA | — | — | — |
| Random Forest | — | — | — |
| LightGBM | — | — | — |

## Author

**Francisco Giordano Rigon**  
Information Systems @ UNISINOS  
Advisor: Prof. Dr. Josiane Brietzke Porto

---

*Undergraduate thesis (TCC) — Defense: June 2026*
