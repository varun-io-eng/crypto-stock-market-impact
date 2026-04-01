# Crypto Impact on Global Stock Market Indices

> **Research Project** — Analysing the impact of cryptocurrency markets on stock market indices across 10 developed and 8 emerging economies.

---

## Overview

This project investigates whether cryptocurrency returns (Bitcoin, Ethereum, Tether/USDT, etc.) have a statistically significant impact on major stock market indices across 18 countries. Each country is analysed using two complementary econometric approaches implemented in Google Colab notebooks.

---

## Countries Covered

### Developed Economies (10)
| Country | Stock Index |
|---------|------------|
| USA | S&P 500 |
| UK | FTSE 100 |
| Germany | DAX |
| Japan | Nikkei 225 |
| France | CAC 40 |
| Canada | TSX Composite |
| Australia | ASX 200 |
| South Korea | KOSPI |
| Switzerland | SMI |
| Netherlands | AEX |

### Emerging Economies (8)
| Country | Stock Index |
|---------|------------|
| Turkey | BIST 100 |
| India | NIFTY 50 / BSE Sensex |
| Brazil | Bovespa |
| China | SSE Composite |
| South Africa | JSE All Share |
| Mexico | IPC |
| Russia | MOEX |
| Indonesia | IDX Composite |


---

## Methodology

Each country notebook follows a consistent two-approach framework:

### Approach 1 — Static OLS Regression

A classical Ordinary Least Squares regression to measure the **contemporaneous (same-day) linear relationship** between crypto returns and stock index returns.

```
Stock_Return_t = α + β × Crypto_Return_t + ε_t
```

**Steps performed:**
1. Compute descriptive statistics and Pearson correlation
2. Manually derive the OLS slope (β) and intercept (α)
3. Run `statsmodels` OLS for full inference (SE, t-stat, p-value)
4. Evaluate goodness-of-fit via R²
5. Plot regression line and residual diagnostics

**What β tells us:** A 1% change in crypto return is associated with a β% change in the stock index, holding all else constant.

---

### Approach 2 — Dynamic Time-Series Regression

Extends the static model by incorporating **market memory** — the idea that today's stock return is also influenced by yesterday's return. This controls for momentum/autocorrelation and isolates the *pure* crypto effect.

```
Stock_Return_t = α + β × Crypto_Return_t + γ × Stock_Return_(t-1) + ε_t
```

**Steps performed:**
1. ADF (Augmented Dickey-Fuller) test for stationarity
2. Lag-1 and Lag-2 autocorrelation analysis of stock returns
3. Estimate dynamic model with lagged dependent variable
4. Assess β (crypto effect) and γ (persistence/momentum)
5. **Granger Causality Test** — does crypto *lead* the stock index?
6. Durbin-Watson diagnostic for residual autocorrelation
7. Plot actual vs fitted, residuals over time, and ACF of residuals

**What γ tells us:** What fraction of yesterday's stock return persists into today's return, independent of crypto.

---

### Why Two Approaches?

| Feature | Approach 1 (Static OLS) | Approach 2 (Dynamic) |
|---|---|---|
| Accounts for market memory | ❌ No | ✅ Yes |
| Controls for autocorrelation | ❌ No | ✅ Yes |
| Tests Granger causality | ❌ No | ✅ Yes |
| Interpretability | ✅ Simple | Moderate |
| Risk of biased β | Higher | Lower |

The dynamic model is generally preferred for financial time-series data. Approach 1 serves as a benchmark.

---

## Repository Structure

```
crypto-stock-market-impact/
│
├── README.md
│
├── notebooks/                    # One Colab notebook per country
│   ├── developed/
│   │   ├── usa_sp500.ipynb
│   │   ├── uk_ftse100.ipynb
│   │   └── ...
│   └── emerging/
│       ├── turkey_bist100.ipynb
│       ├── india_nifty50.ipynb
│       └── ...
│
├── data/
│   ├── crypto/                   # Raw CSV files for crypto assets
│   │   ├── bitcoin_BTC.csv
│   │   ├── ethereum_ETH.csv
│   │   ├── tether_USDT.csv
│   │   └── ...
│   └── stock_indices/            # Raw CSV files for stock indices
│       ├── developed/
│       │   ├── SP500.csv
│       │   ├── FTSE100.csv
│       │   └── ...
│       └── emerging/
│           ├── BIST100.csv
│           ├── NIFTY50.csv
│           └── ...
│
    
```

---

## Data Sources

- **Crypto prices:** [Investing.com](https://www.investing.com) / [CoinGecko](https://www.coingecko.com) — daily OHLC and % change
- **Stock indices:** [Investing.com](https://www.investing.com) / [Yahoo Finance](https://finance.yahoo.com) — daily closing prices and % change
- **Format:** CSV with columns `Date`, `Price`, `Open`, `High`, `Low`, `Change %`
- **Frequency:** Daily (weekends excluded via inner merge)

---

## Setup & Usage

### Running a Notebook in Google Colab

1. Open [Google Colab](https://colab.research.google.com)
2. Upload the notebook (e.g. `notebooks/emerging/turkey.ipynb`)
3. Upload the two required CSV files for that country:
   - The stock index CSV 
   - The crypto asset CSV
4. Run all cells (`Runtime → Run all`)

All required packages are installed automatically at runtime:
```
statsmodels, scipy, pandas, numpy, matplotlib
```

### Running Locally

```bash
git clone https://github.com/varun-io-eng/crypto-stock-market-impact.git
cd crypto-stock-market-impact

pip install pandas numpy matplotlib statsmodels scipy
```

---

## Key Outputs Per Notebook

Each notebook produces 4 charts saved as PNG:

| File | Description |
|------|-------------|
| `01_exploratory_analysis.png` | Time-series of returns, scatter plot, 6-month rolling correlation |
| `02_approach1_static_ols.png` | OLS regression fit + residuals vs fitted |
| `03_approach2_dynamic.png` | Actual vs fitted, residuals over time, ACF of residuals |
| `04_comparison_summary.png` | Side-by-side bar charts comparing β, t-stat, and R² across both approaches |

---

## Econometric Considerations & Limitations

1. **Stablecoin Peg Issue:** Assets like USDT have near-zero variance in returns (pegged to $1). Regressing a highly volatile index against a flat-lined asset will structurally yield non-significant results — not a failure of methodology, but a reflection of the asset's nature.

2. **Spurious Regression Risk:** Financial time-series can appear correlated due to common macroeconomic trends rather than a true causal link. ADF tests and stationarity checks (Approach 2) help mitigate this.

3. **Causality vs Correlation:** OLS (Approach 1) establishes association, not causation. Granger causality tests (Approach 2) provide a stronger (but still statistical, not structural) causal argument.

4. **Country-Specific Drivers:** Emerging market indices are often driven by local monetary policy, currency devaluation, and inflation — factors orthogonal to crypto markets.

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3 | Core language |
| pandas | Data loading, merging, cleaning |
| numpy | Manual OLS derivation |
| statsmodels | OLS, ADF, Granger causality, Durbin-Watson |
| matplotlib | All visualisations |
| Google Colab | Interactive execution environment |

---

## Author

**Varun Khera**


---

## License

This project is for academic and research purposes only.
