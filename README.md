# Customer Segmentation Using RFM Analysis & K-Means Clustering

## Overview
This project segments e-commerce customers based on their purchasing behavior using **RFM analysis** (Recency, Frequency, Monetary value) combined with **K-Means clustering**. The goal is to identify distinct customer groups and translate them into actionable marketing and retention strategies.

## Dataset
**Online Retail** — UCI Machine Learning Repository (Chen, D., 2015), licensed under CC BY 4.0.
Transactional data from a UK-based online retailer, covering all purchases between December 2010 and December 2011 (~540,000 transactions).

## Approach
1. **Data cleaning** — filtered to UK transactions, removed cancelled orders (negative quantities) and rows with missing Customer IDs, and restricted the analysis to a trailing 12-month window calculated dynamically from the dataset's most recent transaction date.
2. **RFM feature engineering** — calculated Recency (days since last purchase), Frequency (number of distinct invoices), and Monetary value (total spend) per customer.
3. **Log transformation** — applied to correct for the heavy right-skew in Frequency and Monetary values before clustering, since untransformed extreme values would otherwise dominate distance-based clustering.
4. **K-Means clustering** — tested cluster counts from k=2 to k=9, evaluated with the elbow method and silhouette scores, then compared k=2, k=3, and k=4 directly on interpretability and business usefulness.
5. **DBSCAN comparison** — tested as an alternative to confirm K-Means was the more practical choice for this use case, since DBSCAN left ~549 points unassigned as noise rather than segmenting the full customer base.

## Key Findings

### At k=2 (baseline)
| Segment | Avg Recency | Avg Frequency | Avg Monetary | Customers | Revenue Share |
|---|---|---|---|---|---|
| Champions | 26.0 days | 7.8x | $671.80 | 1,529 (39.6%) | **92.4%** |
| At Risk / Low-Value | 127.6 days | 1.7x | $36.30 | 2,331 (60.4%) | 7.6% |

Nearly 40% of customers generate over 92% of total revenue — a Pareto-like concentration that makes clear which customers matter most to retain.

### Refining to k=3 (recommended for targeting)
The 2-cluster split was too coarse for a real campaign — it grouped 1,597 moderately-engaged customers in with truly lapsed ones. Splitting into three clusters surfaced a distinct, actionable middle segment:

| Segment | Avg Recency | Avg Frequency | Avg Monetary | Customers |
|---|---|---|---|---|
| Champions | 7.3 days | 11.1x | $1,011.90 | 722 |
| At-Risk-but-Recoverable | 71.5 days | 3.7x | $222.60 | 1,597 |
| Lost / Churned | 141.2 days | 1.3x | $16.60 | 1,541 |

k=4 was also tested (silhouette 0.307 vs. k=3's 0.305) but produced a harder-to-act-on split between "recent, low-spend" and "new" customers, so k=3 was chosen as the better trade-off between statistical fit and business usability.

## Business Recommendations
- **Champions**: Protect this segment with loyalty perks, early access to new products, and personalized outreach — losing even a handful has an outsized revenue impact.
- **At-Risk-but-Recoverable**: The highest-leverage segment to act on. A win-back campaign (targeted discount, "we miss you" email) is far cheaper than acquiring new customers and can recover meaningful revenue before these customers fully lapse.
- **Lost/Churned**: Deprioritize for anything beyond low-cost, automated reactivation emails — the return on heavier investment here is low.

## Tech Stack
- Python (pandas, NumPy, scikit-learn)
- Matplotlib / Seaborn for visualization
- Jupyter Notebook

## How to Run
1. Download the dataset from the [UCI Online Retail page](https://archive.ics.uci.edu/dataset/352/online+retail)
2. Place `Online Retail.xlsx` in the project folder
3. Install dependencies: `pip install pandas numpy scikit-learn matplotlib seaborn openpyxl`
4. Run `Customer_Segmentation_using_big_data_analytics.ipynb` top to bottom

## Notes
This project builds on an open-source reference structure, with fixes and extensions added: a corrected date-filtering bug in the RFM window calculation, full cluster profiling with real revenue-contribution analysis, a comparison of k=2/3/4 cluster solutions, and a business-recommendation writeup based on the actual clustering output.
