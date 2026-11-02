# UCI Retail Sales KPI Dashboard (Python → Excel)
  ![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white) ![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white) ![Jupyter](https://img.shields.io/badge/Jupyter-F37626?logo=jupyter&logoColor=white) ![openpyxl](https://img.shields.io/badge/openpyxl-Excel%20output-217346)

Python pipeline which cleans 540k UK online retail transactions, true net sales are separated from cancellations, fees and adjustments. An audit-logged Excel KPI dashboard is generated in one run as well.

![UCI Retail KPI Dashboard](images/dashboard.png)

## The Problem
Exports of raw sales are deceptive. Real product sales are mixed with cancellations, postage, Amazon fees, bank costs, bad-debt adjustments, human account updates, duplicate rows, and an incomplete final month in this dataset from a UK online gift wholesaler. The totals are incorrect when the revenue column is summed as is.

One issue is addressed by this project: **How did net product sales perform from December 2010 to November 2011, and where did the revenue originate from?** Every transaction is categorised, every exclusion is documented, and a ready-to-open Excel dashboard is produced.

## Findings
1. Over a 12-month period, net sales was £9.23 million, with strong Q4 seasonality. In September to November of 2011, £3.49 million (37.9%) was supplied. Compared to December 2010 (£755k), November 2011 (£1.43M) was 89% more.
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
- Row share, £ impact, keep/remove decision, and written justification for each category are all included on the audit log page.
- Four KPI cards, a monthly revenue trend, the top ten goods, and a non-UK revenue breakdown by region are all displayed as native, editable Excel charts in this one-run Excel dashboard.

## How to Run
1. Install Python 3.12+ and run `pip install -r requirements.txt`
2. Download the dataset from [UCI: Online Retail](https://archive.ics.uci.edu/dataset/352/online-retail) and place `Online Retail.xlsx` next to the notebook.
3. Open `UCI Retail Sales.ipynb` → Run All → `Dashboard.xlsx` is generated.

## Known Limitations
- **Total Customers (7,681) is an upper bound.** Guest checkouts do not have a CustomerID, so each guest invoice is counted as a separate customer: 4,297 registered customers + 3,384 guest orders.
- Duplicate rows are assumed to be system error, some may be genuine repeat line items.

## Tech Stack
- Python is the programming language used.
- Pandas and NumPy are used for data processing.
- Openpyxl is used for reporting and output (native Excel charts, tables, and formatting). Excel from Microsoft
- Jupyter Notebook is the environment.
- The UCI Machine Learning Repository, Online Retail (id 352) is the data source.
Chen, D. (2015). Online Retail [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33.
