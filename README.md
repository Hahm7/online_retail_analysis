# Online Retail Analysis



## Executive Summary
A UK online retailer wants to know how the business has performed over the past 2 years and where it should be focusing next. To answer this, I used SQL in JupyterLab through DuckDB to clean and analyse the dataset and Power BI and Claude AI to create visualisations. Revenue growth came from larger orders rather than more orders and a small group of customers generates most of it. I also identified that those with smaller first orders are the least likely to buy again. I recommend that the sales and marketing teams focus on:

1. Named contact for the top accounts driving revenue
2. Follow up email for those with small first order purchase

---

## Business Problem

Revenue is up 2.5% year on year but orders fell 4% and the business increasingly ran on returning customers as new intake declined. Average order value rising by 6.8% confirms that revenue was held by selling more to the customers the business already has.

![As new customer intake fell away, returning customers climbed to take its place](/docs/img/new_vs_returning_customers.png)

The questions that matter to the commercial team:

- **Who the customers are?**
- **Which of the new customers are worth keeping?** 
- **When are purchases typically made?** 

---

## Methodology

1. **SQL queries** that clean and standardise 1,067,371 raw transactions into an analysis ready table, removing duplicates, invalid prices and non-product stock codes.
2. **Exploratory data analysis** in SQL covering revenue decomposition, RFM segmentation, cohort analysis on first order value, and seasonality by market, month, day and hour.
3. **Visualisations** built in Power BI and Claude AI.

SQL queries was run through DuckDB so they could output in JupyterLab.


## Skills Used

**SQL:** CTEs, window functions (`NTILE`, `ROW_NUMBER`, `LAG`, `FIRST_VALUE`, partitioned `SUM`), `CASE` segmentation, `ROLLUP`, `QUANTILE_CONT`, date and interval functions, joins, aggregates

**Python / JupyterLab:** DuckDB, pandas, dynamic query construction

**Power BI:** DAX measures, data visualisation

**Techniques:** EDA, revenue decomposition, RFM segmentation, concentration analysis, acquisition analysis, seasonality, returns analysis

---