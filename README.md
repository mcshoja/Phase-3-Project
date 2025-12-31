# Weekly Stock Outperformance Screening Using S&P 500 Data

## Overview

This project builds a **classification-based weekly screening model** that identifies S&P 500 stocks likely to **outperform the S&P 500 index over the next five trading days**.
The solution is designed as a **decision-support tool** for a retail trading platform, prioritizing **recall of outperforming stocks** to minimize missed short-term opportunities.

---

## Business Problem

Retail investors engaging in short-term positioning face difficulty identifying stocks that will outperform the broader market in the near term.
The platform’s key risk is **missing stocks that actually go on to outperform**, which reduces user trust and engagement.

**Objective:**
Develop a weekly screener that ranks stocks by their probability of outperforming the S&P 500 over the next trading week.

---

## Data Description

The project uses publicly available market data covering:

* **Stock-level daily prices** for S&P 500 constituents
* **S&P 500 index prices**
* **Company metadata**, including sector classification

After cleaning and feature engineering, the final modeling dataset contains:

* **~420,000 observations**
* **172 stocks**
* **38 engineered features**
* **Binary forward-looking target**

---

## Target Definition

The target variable is defined as:

* **1 (Outperform):** Stock’s 5-day forward return exceeds the S&P 500’s 5-day forward return
* **0 (Not Outperform):** Otherwise

The resulting class distribution is well balanced (~51% outperform / ~49% not outperform), allowing metric selection to be driven by business priorities rather than technical imbalance.

![Image](https://i.postimg.cc/Z5MMdt14/Forward-5-Trading-Day-Outperformance.png)

---

## Exploratory Data Analysis

EDA confirms the **noisy and volatile nature of short-term equity returns**, reinforcing the need for probabilistic screening rather than point prediction.

* Stock returns exhibit heavier tails than the index
* Volume is highly skewed, motivating log transformation
* Sector-level outperformance rates vary modestly, suggesting sector effects are secondary to stock-specific behavior

![Image](https://i.postimg.cc/dQ5m1RbG/Distribution-of-Log(1-Volume.png))

---

## Feature Engineering

Key features were engineered using strictly backward-looking information:

* **Price-based features:** rolling returns, rolling volatility, moving averages
* **Relative performance:** excess returns vs the index
* **Volume features:** short-term and medium-term rolling volume
* **Categorical encoding:** sector indicators

All features were constructed to avoid data leakage and aligned to a calendar-consistent weekly horizon.

---

## Modeling Approach

A time-aware train/test split was used to prevent look-ahead bias.
Models were evaluated primarily on **recall for outperforming stocks**, with supporting metrics including precision, F1 score, accuracy, and ROC-AUC.

### Models Evaluated

* **Logistic Regression (baseline, interpretable)**
* **Decision Tree (default)**
* **Decision Tree (tuned and pruned)**

---

## Model Performance Summary

| Model                   | Recall (Outperform) | ROC-AUC  | Accuracy |
| ----------------------- | ------------------- | -------- | -------- |
| **Logistic Regression** | **0.87**            | **0.88** | **0.81** |
| Decision Tree (default) | 0.77                | 0.73     | 0.73     |
| Decision Tree (tuned)   | 0.99                | 0.55     | 0.50     |

Logistic regression demonstrates the strongest balance of **recall, stability, and ranking power**.

![Image](https://i.postimg.cc/cJdrw2Y7/Logistic-Regression-ROC-Curve.png)

---

## Model Interpretation

Logistic regression coefficients indicate that **short-term price positioning** is the primary driver of weekly outperformance:

* Current and forward-aligned adjusted prices
* Short moving averages (5-day, 10-day)
* Relative performance vs the index (secondary signal)

Decision trees concentrate almost entirely on raw price levels and fail to generalize when regularized, highlighting the instability of nonlinear threshold-based models for this task.

---

## Recommendations

* Deploy the model as a **weekly stock screening tool**, not an automated trading system
* Rank stocks by predicted probability of outperformance
* Surface **top-ranked candidates** (e.g., top 10–20%) to users
* Combine model output with human judgment or additional analysis

---

## Limitations and Future Work

* Relies solely on historical price, volume, and sector data
* Performance may vary across market regimes
* Future extensions could explore regime-aware modeling or carefully regularized ensemble methods

---

## Conclusion

This project demonstrates a full, end-to-end machine learning workflow applied to a realistic financial screening problem.
Through disciplined model comparison and business-aligned evaluation, **logistic regression** emerges as a **robust, interpretable, and deployable solution** for weekly stock outperformance screening.
