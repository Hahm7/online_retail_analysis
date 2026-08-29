# Online Retail Analysis

---

## Executive Summary
A UK online retailer wants to know how the business has performed over the past 2 years and where it should be focusing next. To answer this, I used SQL in JupyterLab through DuckDB to clean and analyse the dataset and Power BI and Claude AI to create visualisations. Revenue growth came from larger orders rather than more orders and a small group of customers generates most of it. I also identified that those with smaller first orders are the least likely to buy again. I recommend that the sales and marketing teams focus on:

1. Named contact for the top accounts driving revenue
2. Follow up email for those with small first order purchase

---

## Business Problem

The online retailer sells giftware and has 2 years of transaction history. Revenue is up 2.5% year on year but orders fell 4% and the business increasingly ran on returning customers as new intake declined. Average order value rising by 6.8% confirms that revenue was held by selling more to the customers the business already has.

The questions that matter to the commercial team:

- **Who the customers are?**
- **Which of the new customers are worth keeping?** 
- **When are purchases typically made?** 

---

## Methodology

- **Cleaned** 1,021,271 transactions in SQL — deduplication, cancellation handling, non-product stock code removal
- **EDA** on revenue, decomposed year on year movement into orders, average order value and active customers
- **RFM segmentation** to size and rank the customer base
- **Cohort analysis** on first order value against repeat purchase
- **Geographic and seasonality analysis** by market, month, day and hour
- **Visualised** in Power BI and Claude AI

Written entirely in SQL, run through DuckDB so queries and output sit together in the notebook.


## Skills Used

**SQL:** CTEs, window functions (`NTILE`, `ROW_NUMBER`, `LAG`, `FIRST_VALUE`, partitioned `SUM`), `CASE` segmentation, `ROLLUP`, `QUANTILE_CONT`, date and interval functions, joins, aggregates

**Python / JupyterLab:** DuckDB, pandas, dynamic query construction

**Power BI:** DAX measures, data visualisation

**Techniques:** EDA, revenue decomposition, RFM segmentation, concentration analysis, acquisition analysis, seasonality, returns analysis

---