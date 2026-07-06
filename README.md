# Time Series Forecasting for Portfolio Management Optimization

**GMF Investments**  
*Author: Meron T. Gebru*  
*Date: July 2026*

[![CI](https://github.com/meronteklehaymanotgebru/portfolio-optimization/actions/workflows/unittests.yml/badge.svg)](https://github.com/meronteklehaymanotgebru/portfolio-optimization/actions)

---

## 1. Business Context

GMF Investments is a forward‑thinking financial advisory firm that leverages cutting‑edge technology and data‑driven insights to deliver tailored investment strategies. This project builds an **end‑to‑end time series forecasting pipeline** that enhances portfolio management by:

1. Extracting and analysing historical data for Tesla (TSLA), Vanguard Bond ETF (BND), and S&P 500 ETF (SPY).
2. Building ARIMA and LSTM models to predict Tesla’s future price.
3. Generating a 12‑month forecast with uncertainty bounds.
4. Applying **Modern Portfolio Theory** to construct an optimal asset allocation.
5. **Backtesting** the optimal portfolio against a passive benchmark.

The final output is a clear, actionable investment recommendation supported by quantitative evidence.

---

## 2. Project Objectives & Status

| Task | Description | Status |
|------|-------------|--------|
| Task 1 | Data Extraction, Cleaning, and EDA | Complete |
| Task 2 | ARIMA & LSTM Model Building | Complete |
| Task 3 | 12‑Month Future Forecast with Confidence Intervals | Complete |
| Task 4 | Portfolio Optimization using MPT | Complete |
| Task 5 | Strategy Backtesting vs Benchmark | Complete |

---

## 3. Data

- **Source:** YFinance Python library  
- **Assets:** TSLA (Tesla), BND (Vanguard Bond ETF), SPY (S&P 500 ETF)  
- **Period:** 2015‑01‑01 to 2026‑06‑30  
- **Output:** Cleaned closing prices saved to `data/processed/asset_prices.csv`

---

## 4. Setup & Installation

### 4.1 Prerequisites
- Python 3.10+
- Git

### 4.2 Installation
```bash
git clone https://github.com/meronteklehaymanotgebru/portfolio-optimization.git
cd portfolio-optimization
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 4.3 Running the Project
Open and run the master notebook:
```bash
jupyter notebook notebooks/portfolio_analysis.ipynb
```
This single notebook executes all five tasks sequentially, generating all figures, CSV outputs, and model artifacts.

---

## 5. Repository Structure

```
portfolio-optimization/
├── .github/
│   └── workflows/
│       └── unittests.yml          # CI pipeline (flake8 + pytest)
├── data/
│   └── processed/                 # Cleaned price data, forecasts, metrics
├── notebooks/
│   └── portfolio_analysis.ipynb   # End‑to‑end notebook
├── reports/
│   ├── images/                    # EDA, forecast, frontier, backtest plots
│   ├── interim_report.md
│   └── final_report.md
├── src/
│   └── __init__.py
├── tests/
│   └── test_dummy.py
├── scripts/
│   └── __init__.py
├── requirements.txt
├── README.md
└── .gitignore
```

---

## 6. Key Findings

### 6.1 Exploratory Data Analysis (Task 1)

- **TSLA** exhibits extreme volatility and high growth; **BND** is stable; **SPY** shows steady appreciation.  
- All closing prices are **non‑stationary** (ADF test, p > 0.05), but daily returns are **stationary** (p ≈ 0). This confirms that differencing (d=1) is required for ARIMA.  
- **Risk Metrics (annualized):**  
  - TSLA: VaR(95%) = -0.0048, Sharpe Ratio = -0.0008  
  - BND: VaR(95%) = -0.0167, Sharpe Ratio = 0.7042  
  - SPY: VaR(95%) = -0.0517, Sharpe Ratio = 0.7595  

*Key insight:* TSLA alone does not offer attractive risk‑adjusted returns; diversification is essential.

### 6.2 Time Series Forecasting (Tasks 2 & 3)

#### ARIMA(1,1,1)
- Fitted on TSLA training data (2015‑2024).  
- Test performance (2025‑2026):  
  - **MAE = 3.46**, **RMSE = 3.87**, **MAPE = 4.79%**  
- 12‑month future forecast: The model predicts a relatively **flat trajectory**, reflecting ARIMA’s mean‑reverting nature. Confidence intervals widen significantly with horizon.

#### LSTM (preliminary)
- A two‑layer LSTM with dropout was trained on 60‑day sequences.  
- Test performance: **MAE = 0.22**, **RMSE = 0.28**, **MAPE = 0.31%** – suspiciously low, suggesting possible overfitting or data leakage.  
- For the final portfolio, the **ARIMA forecast is used** because of its interpretability and conservative nature.

### 6.3 Portfolio Optimization (Task 4)

Using **PyPortfolioOpt**, the Efficient Frontier was constructed with:
- TSLA expected return derived from the ARIMA 12‑month forecast (≈ -0.02% annual).  
- BND and SPY expected returns from historical averages (14.3% and 45.4% annual, respectively).  
- Annualized covariance matrix from historical daily returns.

**Optimal Portfolio (Maximum Sharpe Ratio):**  
- Weights: **TSLA 0.00%, BND 72.14%, SPY 27.86%**  
- Expected annual return: **7.42%**  
- Expected annual volatility: **15.97%**  
- Sharpe Ratio: **0.3395**

*Interpretation:* The optimizer effectively excludes Tesla due to its negligible expected return and high volatility, and concentrates on the stable, high‑return combination of bonds and the S&P 500.

### 6.4 Backtesting (Task 5)

The optimal portfolio was backtested over **2025** (out‑of‑sample) and compared to a benchmark of **60% SPY / 40% BND**.

| Metric | Strategy (Optimized) | Benchmark (60/40) |
|--------|----------------------|-------------------|
| Total Return | 21.35% | 22.17% |
| Annual Return | 21.63% | 22.47% |
| Annual Volatility | 29.17% | 43.72% |
| Sharpe Ratio | **0.673** | 0.468 |
| Max Drawdown | -26.67% | -36.55% |

*Conclusion:* The optimized portfolio achieved a **higher Sharpe Ratio** and **lower maximum drawdown** than the benchmark, demonstrating superior risk‑adjusted performance despite a slightly lower total return.

---

## 7. Repository Best Practices

- **Branching workflow:** `task-1`, `task-2`, etc., merged into `main` via Pull Requests.  
- **CI/CD:** GitHub Actions runs `flake8` linting and `pytest` on every push.  
- **Commit conventions:** [Conventional Commits](https://www.conventionalcommits.org/) used throughout.  
- **Documentation:** Comprehensive README, inline code comments, and final report.

---

## 8. How to Use This Project

1. Clone the repository and install dependencies.
2. Run `notebooks/portfolio_analysis.ipynb` from top to bottom.
3. All outputs (charts, CSV files) will be saved in `reports/images/` and `data/processed/`.
4. The final report (`reports/final_report.md`) summarizes the entire workflow.

---

## 9. References

- [YFinance Documentation](https://pypi.org/project/yfinance/)
- [Statsmodels ARIMA](https://www.statsmodels.org/stable/generated/statsmodels.tsa.arima.model.ARIMA.html)
- [PyPortfolioOpt](https://pyportfolioopt.readthedocs.io/)
- [Investopedia: Modern Portfolio Theory](https://www.investopedia.com/terms/m/modernportfoliotheory.asp)

---

**Author:** Meron T. Gebru  
**Challenge:** 10 Academy, Week 9  
**Date:** July 2026
