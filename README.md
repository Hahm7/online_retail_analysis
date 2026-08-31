# Customer Segmentation and Revenue Analysis

---

## Executive Summary
A UK online retailer wants to know how the business has performed over the past 2 years and where it should be focusing next. To answer this, I used SQL in JupyterLab through DuckDB to clean and analyse the dataset and Power BI and Claude AI to create visualisations. Revenue growth came from larger orders rather than more orders and a small group of customers generates most of it. I also identified that those with smaller first orders are the least likely to buy again. I recommend that the sales and marketing teams focus on:

1. Named contact for the top accounts driving revenue
2. Follow up email for those with small first order purchase



## Business Problem

### The headline
| | Year 1 (Dec 09 to Nov 10) | Year 2 (Dec 10 to Nov 11) | Change | Two years |
|---|---|---|---|---|
| Revenue | £9,397,599 | £9,632,728 | +2.5% | £19,030,328 |
| Orders | 19,756 | 18,957 | −4.0% | 38,713 |
| Average order value | £475.68 | £508.14 | +6.8% | £491.57 |
| Active customers | 4,239 | 4,293 | +1.3% | |

Across the two complete trading years, revenue grew by 2.5% but orders fell by 4%. With new customer intake
declining over the same period and the average order value rising by 6.8%, this suggests the
growth came from selling more to the people already there.

![As new customer intake fell away, returning customers climbed to take its place](/docs/img/new_vs_returning_customers.png)

The questions that matter to the commercial team:

- **Who the customers are?**
- **Which of the new customers are worth keeping?** 
- **When are purchases typically made?** 


## Methodology

1. **SQL queries** that clean and standardise 1,067,371 raw transactions into an analysis ready table.Duplicates, invalid prices and non-product stock codes removed.
2. **Exploratory data analysis** in SQL covering revenue decomposition, RFM segmentation, cohort analysis on first order value, and seasonality by market, month, day and hour.
3. **Visualisations** built in Power BI and Claude AI.

SQL queries were run through DuckDB to output in JupyterLab.


## Skills Used

**SQL:** GROUP BY, CTEs, window functions (`NTILE`, `ROW_NUMBER`, `LAG`, `FIRST_VALUE`, partitioned `SUM`), `CASE` segmentation, `ROLLUP`, `QUANTILE_CONT`, views, date and interval functions, joins, aggregates

**Python / JupyterLab:** DuckDB, pandas

**Power BI:** DAX measures, data visualisation

**Techniques:** EDA, revenue decomposition, RFM segmentation, concentration analysis, acquisition analysis, seasonality, returns analysis


## Results & Recommendations

To look at where revenue comes from, I scored and segmented every customer, ranked the
markets, split new customers by first order value and broke revenue down by month, day and
hour.

---

### Who the customers are

![Champions make up 22% of customers but generate 68% of total customer revenue](/docs/img/rfm_segments.png)

| | |
|---|---|
| Champions | 1,310 |
| Revenue | £11,669,509 |
| Average spend | £8,908 |
| Median spend | £3,829 |
| Top 5 share of segment revenue | 16.5% |
| Top 25 share of segment revenue | 33.8% |

**Champion customers** is the segment that buys often and spends the most, they make up **22% of customers** but generate **68% of customer revenue.** This segment is also skewed internally as half of them spend under **£3,829** while the average is **£8,908** and the **top 25 accounts hold about 34% of the segment's revenue**.

**Recommendation:** Give the top 25 accounts a named contact in sales who checks in regularly, so problems surface before an account is lost.


Geography tells the same story. The **UK is 86% of revenue (£16.8m)**, and most of the international markets are dominated by a single customer:

![UK is 86% of revenue](/docs/img/top_rev_country.png)

![Netherlands, EIRE and Australia revenue is dominated by a single customer](/docs/img/international_market_rev.png)

| Market | Revenue | Customers | Cancellation rate | Revenue Concentration |
|---|---|---|---|---|
| EIRE | £623k | 3 | 20.1% | 2 customers = 92% |
| Netherlands | £550k | 22 | 4.9% | 1 customer = 96% |
| Germany | £383k | 107 | 27.3% | broad |
| France | £311k | 93 | 15.7% | broad|
| Australia | £168k | 15 | 17.6% | 1 customer = 86% |

**Recommendation:** Invest in EU marketing and fulfillment for France. Look further into Germany's high cancelation rate (27.25%) before taking action

---

### Which of the new customers are worth keeping

![Low first order spenders are least likely to buy again](/docs/img/first_order_value_vs_buy_again.png)

The rate rises at every step, but the sharpest jump is from the bottom quartile to the second. Above roughly £167 the rates are high and similar.

**Recommendation:** Email low spend customers within 2 weeks of first purchase to help encourage a second purchase.

---

### When do customers buy

![November generates the highest average revenue](/docs/img/average_monthly_revenue_chart.png)

November averages **£1.44m**, roughly double a typical month. September and October are already the next highest at £949k and £1.10m, so demand climbs for three months before it peaks.

![Saturday generates less than 1% of weekly revenue](/docs/img/revenue_by_day.png)

This is a weekday business as Monday to Friday carries around 90% of revenue and Saturday takes just £9,803 across the entire two years.

![Revenue peaks at 12pm and falls to near zero by 8pm](/docs/img/revenue_by_hour.png)

Within the day, revenue peaks at midday and around 90% is taken between 9am and 5pm.

**Recommendation:** Commit stock and launch campaigns by early September and hold the annual planning review in July. Schedule campaigns and emails for weekday mornings and staff customer service to the 9 to 5 window rather than evenly across the week.


## Next Steps

1. Investigate the reasons behind the fall in new customers
2. Bring in cost data to check whether the biggest accounts are the most profitable
3. Group products into categories to see where returns concentrate

**Limitations:** 22% of transactions have no customer ID, so guest buyers sit outside every customer level finding here and the true repeat rate is likely understated. Product analysis is at item level with no category data. The customer segments come from thresholds I chose rather than learned from the data. With no marketing spend or cost data, the figures above are based on revenue and stated assumptions rather than measured returns. December is excluded from year comparisons because December 2011 has only 8 trading days.


## Repository Structure

```
online_retail_analysis/
├── README.md
├── notebooks/          # Audit, cleaning and analysis notebooks
├── docs/img/           # Charts used in this README
├── chart.umd.js        # Chart.js, loaded locally for notebook rendering
├── requirements.txt
└── .gitignore          # data/ excluded — see source below
```

**Data source:** [UCI Machine Learning Repository — Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii). Transactions from 1 December 2009 to 9 December 2011.

