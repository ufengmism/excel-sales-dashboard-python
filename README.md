# excel-sales-dashboard-python
  ![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white) ![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white) ![Jupyter](https://img.shields.io/badge/Jupyter-F37626?logo=jupyter&logoColor=white) ![openpyxl](https://img.shields.io/badge/openpyxl-Excel%20output-217346)

<img width="475" height="310" alt="Screenshot 2026-11-02 123523" src="https://github.com/user-attachments/assets/39d45182-b1ea-4bef-bcfe-2405c9c667fd" align="center"/>

## The Problem
Exports of raw sales are deceptive. Real product sales are mixed with cancellations, postage, Amazon fees, bank costs, bad-debt adjustments, human account updates, duplicate rows, and an incomplete final month in this dataset from a UK online gift wholesaler. The totals are incorrect when the revenue column is summed as is.

One issue is addressed by this project: **How did net product sales perform from December 2010 to November 2011, and where did the revenue originate from?** Every transaction is categorised, every exclusion is documented, and a ready-to-open Excel dashboard is produced.

## Findings
1. Over a 12-month period, net sales was £9.23 million, with strong Q4 seasonality. In September and November of 2011 alone, £3.49 million (37.9%) was supplied. Compared to December 2010 (£755k), November 2011 (£1.43M) was 89% more.
2. Income is focused by country: Approximately 85% of income comes from the UK. The Netherlands (£271k), EIRE (£247k), Germany (£194k), and France (£176k) are the biggest non-UK markets.
3. The top product, Regency Cakestand 3 Tier, brought in £158.6k. Revenue is long-tail by product. Together, the top ten goods accounted for just around 8% of net sales.
4. Cancellations must be netted out rather than disregarded because they counterbalance £457k, or around 4.7% of gross product sales.

## Why this Architecture?
For categorisation, **`pandas` + `np.select`** are utilized. Each row receives precisely one label since rules are executed in priority order. If any row remains unclassified, an assertion ends the run.

As for the output, I used **`openpyxl`**. The dashboard is a native.xlsx file that contains actual Excel tables and charts rather than pictures. Stakeholders may still change the charts when they access it in Excel without a Python or Power BI license.

Instead of using quiet filtering, a log sheet is used. Each category displays its percentage of rows, its effect on £, whether it was retained, and why.

**Trade-offs** 
- A notebook is more difficult to schedule than a script or CLI, but it is easier to understand and audit.
- Charts display row locations that are determined during runtime. The chart references must be updated if the tables' shapes change.
- On around 540k rows, `read_excel` is sluggish. Parquet or CSV would load more quickly.
- A static snapshot of the dataset is the output. If the dataset is refreshed, re-run the notebook.

## Key Features
- Eight priority-ordered categories (sales, cancellations, costs, duplicates, bad debt, manual adjustments with or without customer ID, unfinished month, and zero-unknown check) comprise the rule-based transaction classification.
- Postage, fees, bank charges, bad debt, and duplicates are not included in business-correct net sales; cancellations are netted out.
- Row share, $ impact, keep/remove decision, and written justification for each category are all included on the audit log page.
- Four KPI cards, a monthly revenue trend, the top ten goods, and a non-UK revenue breakdown by region are all displayed as native, editable Excel charts in this one-run Excel dashboard.

## Tech Stack
- Python is the programming language used.
- Pandas and NumPy are used for data processing.
- Openpyxl is used for reporting and output (native Excel charts, tables, and formatting). Excel from Microsoft
- Jupyter Notebook is the environment.
- The UCI Machine Learning Repository, Online Retail (id 352) is the data source.
Chen, D. (2015). Online Retail [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33.
