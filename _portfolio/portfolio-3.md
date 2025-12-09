---
title: "Major Market Rallies Prediction"
excerpt: "Predicting Major Market Rallies for Asymmetric Risk Management <br/><img src='/images/portfolio/market_rallies_prediction/market_rallies_prediction.png' style='width: 50%; height: auto;'>"
collection: portfolio
---
## 1\. Executive Summary

This project was developed in response to a challenge by **ETS Asset Management Factory** during the **Zrive Applied Data Science** program. The objective was to build a machine learning model capable of anticipating sudden, high-magnitude market rallies.

The ultimate goal was to generate a predictive signal that allows conservative investment portfolios to capture "upside" potential during market rebounds without compromising their low-risk profile. The proposed implementation involves using these signals to trigger the purchase of Call options, creating an asymmetric risk profile (capped loss, uncapped gain).

## 2\. The Business Problem

Conservative investment portfolios often use hedging strategies to limit **drawdowns** (losses) during market crashes. However, a major pain point arises during the recovery phase:

  * **The Problem:** Protective strategies often dampen returns during rapid "V-shaped" recoveries. Clients are satisfied with protection during the crash but become dissatisfied when their portfolio lags behind the market during the subsequent high-visibility rally.
  * **The Goal:** Improve the **Upside Capture Ratio** while maintaining a low Downside Capture Ratio.
  * **The Solution:** Identify specific time windows where the market is statistically likely to surge, allowing the fund to layer on aggressive, limited-risk exposure (Call Options) only when the probability of a rally is high.

<p align="center">
  <img src="/images/portfolio/market_rallies_prediction/runupdrawdown.png" width="70%">
</p>

<p align="center"><em>Figure 1: Historical analysis of S&P 500 "Runups"</em></p>


## 3\. Methodology

### Data & Feature Engineering

The model utilized daily financial data, including S&P 500 Total Return prices, the VIX volatility index, and sector-specific data.

  * **Target Definition:** A binary classification target defined as a market rise of $\ge 6\%$ within a forward-looking 20-session window.
  * **Feature Construction:**
      * **Lagged Returns:** Rolling means and standard deviations (volatility) over 20, 60, and 480 days to capture short and long-term trends.
      * **Drawdown Metrics:** Distance from all-time highs and max drawdowns over varying windows (5d, 15d, etc.).
      * **Technical Indicators:** RSI (Relative Strength Index) and VIX rate of change.
  * **Validation Strategy:** To prevent look-ahead bias—a common pitfall in financial ML—I utilized a **Walk-Forward Validation (Rolling Window)** approach, strictly separating training and validation sets chronologically.

<p align="center">
  <img src="/images/portfolio/market_rallies_prediction/sliding_window_split.png" width="70%">
</p>

<p align="center">
  <em>Figure 2: Time series backtesting strategy using refitting and fixed training sizes to simulate real-world production forecasting.</em><br>
  <em>Source: <a href="https://www.uber.com/en-ES/blog/forecasting-introduction/">Uber Engineering Blog</a></em>
</p>


### Modeling Strategy

The problem is characterized by high noise and a significant class imbalance (only \~7.4% of days preceded a major rally).

1.  **Baselines:** Benchmarked against Random Walks (Monte Carlo simulations), drawdown correlaton, and standard technical strategies (EMA Crossover + RSI).
2.  **Model Selection:** Compared Linear Models (Logistic Regression with Ridge/Lasso regularization) against Non-Linear Models (LightGBM).
3.  **Optimization Metric:** Prioritized **Precision-Recall AUC (PR-AUC)**. In this context, Precision is vital (False Positives cost money in option premiums) and Recall is necessary to ensure we don't miss the rare profitable opportunities.

## 4\. Key Results & Insights

Contrary to the initial hypothesis that complex non-linear models would capture intricate market patterns, **regularized linear models outperformed gradient boosting methods**.

  * **Best Model:** Logistic Regression (L2 penalty, C=0.0001).
  * **Performance:** Achieved a **PR-AUC of 0.361**, significantly outperforming the GBM baseline (0.073) and the LightGBM model (0.290).
  * **Simulated Trading Metrics:** With a decision threshold set to 0.5:
      * **Precision:** \~31% (Roughly 1 in 3 signals resulted in a major rally).
      * **Recall:** \~35% (The model captured over one-third of all major market moves).
      * **False Alarm Rate:** \~5.1%.

<p align="center">
  <img src="/images/portfolio/market_rallies_prediction/PRCurve.png" width="70%">
</p>

<p align="center"><em>Figure 3: Precision-Recall curves demonstrating the Linear Model (Blue) outperforming LightGBM (Orange) and the drawdown baseline (Red).</em></p>

## 5\. Conclusion

This project highlighted that predicting large market jumps is a low signal-to-noise ratio problem. A key insight is that in such environments, simpler, heavily regularized models often generalize better than more complex architectures that tend to overfit.

The final model delivered promising backtesting results, showing improvements over baseline strategies.

However, the model is not yet production-ready. Its effectiveness still needs to be validated in a realistic trading evaluation, stress-testing across market regimes, and assessing execution and option-pricing constraints. Only after confirming robustness in real-time conditions could this signal be reliably integrated into an investment process.

## 6. Credits & Team
This project was a collaborative effort developed by **Antonio García Garví**, **Pedro Tejero**, and **Hugo Nieto**, under the technical mentorship of **Guillermo Barquero**.
