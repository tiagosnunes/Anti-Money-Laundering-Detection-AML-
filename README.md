# Anti-Money-Laundering-Detection-AML-
Multi-method fraud detection system for AML — combining rule-based flags, Benford's Law, clustering, and network analysis to identify smurfing and money mule patterns in transaction data
# AML Fraud Detection — Smurfing & Money Mule Identification

A multi-method analytical framework to detect two common money-laundering schemes — **smurfing** and **money mule** activity — hidden within 15,158 transactions from a 1,000-client banking portfolio.

## Overview

Two fraud schemes were suspected within the transaction data:

- **Smurfing** — splitting large amounts into multiple deposits just below the €10,000 reporting threshold, to avoid regulatory scrutiny.
- **Money mule** — pass-through accounts that receive funds from multiple sources and quickly forward them elsewhere, erasing the trail.

Rather than relying on a single detection method, six independent techniques were applied and cross-validated against each other. The core hypothesis: **the more independent techniques converge on the same client, the higher the confidence in the suspicion — and the lower the risk of a false positive.**

## Methodology

Six independent detection techniques were combined:

| Technique | What it captures |
|---|---|
| **Business rules** | 8 rules based on value, destination, timing, account, and client profile |
| **Threshold analysis** | Distribution of transaction amounts around the €10,000 reporting limit |
| **Benford's Law** | 1st and 2nd digit distribution, validated with MAD and Chi-square tests |
| **Z-score** | Outliers across client behavioral variables |
| **Clustering (K-Means)** | Unsupervised grouping by transaction similarity |
| **Network analysis** | Hub detection, centrality, and bridge clients across the transaction network |

## Key Findings

**Smurfing detection**
Statistical evidence of deliberate amount-splitting: 740 transactions clustered just below €10,000 vs. only 16 just above — a distribution that would be near-symmetric under natural conditions. Benford's Law confirmed the anomaly: 40.5% of deposits started with digit 9 (expected: 4.6%), with a Chi-square p-value < 0.001 on both the 1st and 2nd digit tests.
→ **25 clients identified**, moving ~€7.15M in transactions structured just under the reporting threshold.

**Money mule detection**
Rather than transaction value, mule accounts were identified by network pattern: median of 34 distinct beneficiaries and 23 distinct destinations, versus 12 and 6 respectively for the rest of the portfolio. A K-Means clustering model (k = 3, silhouette score 0.736) independently formed a "mule profile" cluster without any access to the rule-based flags — validating the pattern from a second, unsupervised angle.
→ **77 clients flagged** (22 confirmed via rules, 55 new candidates from clustering/network analysis).

**Network analysis**
Mapped 14,856 client-beneficiary connections. Three convergence hubs were identified, connected not directly but through **2 bridge clients** who operate across multiple hubs simultaneously — turning isolated destination points into a single connected network.

**Final convergence matrix**
Combining all six evidence signals (rule violations, cluster membership, Benford deviation, network position):

| Priority | Clients |
|---|---|
| High | 27 |
| Medium | 25 |
| Low | 50 |
| No alert | 898 |

Validated against known cases in the dataset: **25/25 smurfing** and **22/22 money mule** cases correctly identified, with 10.2% of the portfolio flagged for review.

## Limitations

- **Threshold calibration** — money mule thresholds were tuned against known cases in the dataset; without that ground truth, the same coverage may not be reproducible on new data.
- **Asymmetric difficulty** — smurfing follows clear, repetitive patterns, while money mule behavior depends on account relationships, making it harder to separate from legitimate high-activity clients.
- **Equal evidence weighting** — all six signals currently contribute equally; a supervised model could learn calibrated weights instead of fixing them by judgment.

## Tech Stack

- **Data handling:** `pandas`, `numpy`
- **Statistics:** `scipy.stats` (Chi-square, Shapiro-Wilk, Kruskal-Wallis)
- **Machine Learning:** `scikit-learn` (K-Means clustering)
- **Network analysis:** `networkx`
- **Visualization:** `matplotlib`
- **Environment:** Google Colab / Jupyter Notebook

## Repository Contents

- `A4B_FA_Notebook.ipynb` — full analysis notebook
- `A4B_FA_Relatorio.pdf` — presentation deck with findings and results

## How to Run

1. Clone this repository
2. Open the notebook in Google Colab or Jupyter
3. Upload the dataset file when prompted (`Caso Pratico 3 - AML.xlsx`)
4. Run all cells in order

## Context

Group project developed for the course *"Analytics for Business"* (ISCTE Executive Education)

## Authors

- Ana Rita Taborda
- Artur Albuquerque
- Pedro Franco
- Tiago Nunes
