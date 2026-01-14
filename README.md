# Nonlinear Multi-Factor Stock Selection with XGBoost and Dynamic KNN Neighbors

## Overview
This project develops a nonlinear multi-factor stock selection strategy using **XGBoost** as the core prediction model, augmented with a **K-Nearest Neighbors (KNN)–based dynamic neighbor mechanism**.  
The goal is to overcome the limitations of traditional linear factor models in capturing **nonlinear relationships, high-dimensional interactions, and time-varying market structures**.

The strategy is implemented on the **CSI 300 constituent universe**, evaluated at a **monthly frequency**, and focuses on **cross-sectional return ranking** rather than point prediction accuracy.

---

## Key Contributions
- **Nonlinear factor modeling** using XGBoost to capture complex factor interactions
- **Dynamic neighbor factors** constructed via KNN in feature space, avoiding rigid industry classifications
- **Rolling-window hyperparameter tuning** with Optuna to adapt to market regime changes
- **RankIC-driven optimization**, aligning machine learning objectives with asset pricing theory
- **Model interpretability** through SHAP values and Gain-based feature importance analysis

---

## Data & Universe
- **Stock universe**: CSI 300 constituents  
- **Time period**: Dec 2017 – Dec 2025  
- **Data source**: Tushare Pro  
- **Prediction frequency**: Monthly  
- **Target variable**: Next-month excess return (relative to CSI 300)

Monthly frequency is chosen to reduce high-frequency noise and better exploit fundamental and medium-term momentum signals.

---

## Factor System
The factor set covers four complementary dimensions:

### 1. Momentum Factors
- Short- and long-term price momentum (e.g. ROC, PLRC12)
- Capture trend persistence and continuation effects

### 2. Value Factors
- Valuation ratios (e.g. EP)
- Identify relatively undervalued stocks with potential mean reversion

### 3. Volatility Factors
- Historical volatility and dispersion measures
- Proxy for risk, disagreement, and potential reversal

### 4. Technical Factors
- Price–volume indicators and normalized technical signals
- Example: **Bollinger Position**, standardizing price location within bands across stocks

Together, the factor system balances **trend, valuation, and risk**, allowing the model to distinguish between “strong but expensive” and “cheap but risky” stocks.

---

## Dynamic KNN Neighbor Factors
Instead of relying on static industry classifications, this project introduces **KNN-based dynamic neighbor factors**:

- Stocks are embedded in a **standardized multi-factor feature space**
- For each stock, K nearest neighbors are identified **cross-sectionally at the same time point**
- Self-inclusion is explicitly excluded to prevent data leakage

Constructed features include:
- **KNN mean momentum**
- **KNN return gap** (neighbor average return minus own return)
- Market-adjusted gap to isolate stock-specific effects

**Economic intuition**:
- Capture **peer effects**, sector-style rotation, and short-term “catch-up” dynamics
- Detect cases where similar stocks have moved but the target stock has not

---

## Model & Optimization
- **Model**: XGBoost (tree-based gradient boosting)
- **Prediction task**: Cross-sectional return ranking
- **Validation**: TimeSeriesSplit to respect temporal ordering
- **Hyperparameter tuning**:
  - Rolling-window optimization
  - Bayesian optimization via Optuna (TPE sampler)
- **Objective metric**: **Rank Information Coefficient (RankIC)**

Using RankIC rather than MSE aligns the learning objective with portfolio construction, where **relative ranking matters more than numerical prediction accuracy**.

---

## Portfolio Construction & Risk Control
### Stock Selection
- Monthly ranking based on predicted returns
- **Top 10 stocks** selected each period

### Weighting Schemes
- Equal-weighted portfolio
- Linear rank-based weighting (higher confidence → higher weight)

### Timing & Risk Filters
- Model-based filter: stay in cash when average predicted return < 0
- Volatility-based market filter using CSI 300 volatility percentiles

These mechanisms help control drawdowns during high-risk market regimes.

---
## Results & Interpretation
- The strategy delivers **persistent excess returns** over the CSI 300 benchmark
- Rolling optimization improves out-of-sample stability
- KNN factors contribute additional explanatory power beyond traditional factors

### Interpretability
- **Gain** reflects feature importance during tree construction
- **SHAP values** reveal nonlinear and heterogeneous effects on predictions

Key findings:
- Volatility and size play dominant roles
- Momentum effects are strong but non-monotonic
- KNN gap factors confirm economically intuitive “catch-up” behavior
---
## Future Extensions
- **Dynamic time-series neighbors** using DTW for regime similarity
- **Market state modeling** via Hidden Markov Models (HMM)
- Conditional factor usage under different volatility or regime states
---
## Tools & Libraries
- Python
- XGBoost
- Optuna
- pandas / numpy
- scikit-learn
- SHAP
---
## Disclaimer
This project is for **academic and research purposes only**.
