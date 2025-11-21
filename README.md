# Walk-Forward Optimization Engine: Trend Following Strategy

**A rigorous quantitative analysis of Moving Average crossover strategies on SPY (2020–2024), featuring transaction cost simulation, parameter heatmaps, and Out-of-Sample Walk-Forward testing.**

-----

###  Project Overview

This project builds an end-to-end backtesting framework to evaluate the robustness of trend-following strategies. Unlike standard academic backtests, this engine incorporates **transaction costs (0.10%)** and utilizes **Walk-Forward Optimization** to eliminate look-ahead bias.

The goal was not simply to "find a profitable strategy," but to stress-test how static parameters perform under changing market regimes (e.g., the 2022 Crash vs. the 2023 AI Rally).

<img width="981" height="528" alt="image" src="https://github.com/user-attachments/assets/62ab6e8d-1410-477b-801a-28e3273a4e5c" />


###  Technologies & Skills

  * **Python:** Core logic and data processing.
  * **Pandas & NumPy:** Vectorized backtesting (avoiding slow `for` loops).
  * **Seaborn & Matplotlib:** Heatmaps and equity curve visualization.
  * **yfinance:** Automated data pipeline for market data extraction.
  * **Financial Concepts:** Slippage modeling, Sharpe Ratio, In-Sample vs. Out-of-Sample testing.

-----

###  Methodology

#### 1\. The Core Algorithm

The base strategy is a classic **Dual Moving Average Crossover**:

  * **Long Condition:** Short Window SMA \> Long Window SMA.
  * **Cash Condition:** Short Window SMA \< Long Window SMA.
  * **Constraint:** `Shift(1)` logic applied strictly to prevent look-ahead bias (trading on yesterday's close).
  * **Costs:** A 10bps (0.10%) fee is applied per trade to simulate spread and commission.

#### 2\. In-Sample Optimization (Grid Search)

I ran a permutation of **400 parameter combinations**:

  * Short Window: 10–50 days
  * Long Window: 60–200 days
  * **Objective:** Identify "stable clusters" of profitability rather than a single overfitted peak.

<img width="925" height="722" alt="image" src="https://github.com/user-attachments/assets/76296078-5cff-4edc-b8f5-eec883d691cb" />


*Above: Heatmap showing the stability of returns across parameter ranges. The "Green Cluster" indicates robust parameter regions.*

#### 3\. Out-of-Sample "Walk-Forward" Analysis

To simulate real-world trading, I implemented a dynamic re-optimization loop:

1.  **Train:** Optimize parameters on Years $T-2$ to $T-1$.
2.  **Test:** Apply those parameters to Year $T$ (Unknown Future).
3.  **Repeat:** Roll the window forward annually.

-----

###  Key Results & Analysis

| Metric | Buy & Hold (SPY) | Walk-Forward Strategy |
| :--- | :--- | :--- |
| **Total Return (2020-2024)** | **+57.27%** | +20.67% |
| **Drawdown Characteristics** | High Volatility | **Protected Capital in 2022** |

<img width="981" height="528" alt="image" src="https://github.com/user-attachments/assets/cfaded00-4b89-4df2-9bbd-b41e67b1ec46" />



#### The "Regime Change" Discovery

While the In-Sample tests showed high potential returns (\~66%), the Walk-Forward analysis revealed a critical insight regarding **Market Regimes**:

1.  **Success in Bear Markets:** In 2022, the algorithm correctly identified the downtrend and moved to cash, protecting capital while the market crashed.
2.  **The Lag Problem:** In 2023, the market shifted to a sharp "V-Shape" recovery. Because the model had optimized on the volatile data of 2022, it selected conservative (slow) parameters (e.g., 50/120). This caused the strategy to react too slowly to the 2023 rally, leading to underperformance against the benchmark.

###  Lessons Learned

  * **Parameter Lag:** Static optimization based on past volatility often fails during sharp regime shifts (e.g., Crash to Rally).
  * **Transaction Costs Matter:** High-frequency settings (e.g., 10/20 MA) appeared profitable in raw backtests but became unprofitable once the 0.10% cost per trade was applied.
  * **Robustness \> Profit:** The strategy is an excellent risk management tool (reducing drawdown) but requires a "Regime Detection" overlay to capture rapid upside recoveries.

-----

###  How to Run This Code

1.  Clone the repo:

<!-- end list -->

```bash
git clone https://github.com/I-am-Uchenna/quant-trend-optimization.git
```

2.  Install requirements:

<!-- end list -->

```bash
pip install pandas numpy yfinance matplotlib seaborn
```

3.  Run the Jupyter Notebook `Analysis.ipynb`.

-----

### Next Steps

  * **Regime Detection:** Implement a Volatility Filter (e.g., VIX threshold) to switch between "Fast" and "Slow" parameters dynamically.
  * **Asset Rotation:** Test this trend-following logic on Crypto assets (BTC/ETH), which historically exhibit stronger trends than Equities.

-----
