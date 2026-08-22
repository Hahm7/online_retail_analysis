# Online Retail Customer & Revenue Analysis (SQL)

Two years of transactions from a UK online gift retailer, analysed entirely in SQL, to answer one question: where does the next pound of growth come from?

[Full analysis notebook](notebooks/analysis.ipynb)

---

## Executive Summary

On paper, this is a healthy business. £19.6 million in gross revenue over two years, an average order worth nearly £500 and a return rate below 3%. Look closer and the growth has stopped. Revenue rose just 2.5% year on year while orders fell 4%, and the only thing holding the topline up was customers spending 6.8% more per basket. Fewer people are buying, they are just buying bigger. This analysis of 1.02 million cleaned transactions, written start to finish in SQL (DuckDB), set out to find where growth should come from next.

The answer is concentration. A fifth of identified customers generate 68% of customer revenue, and within that group just 25 accounts hold a third of it. The cheapest leak to fix sits at the other end of the ladder, where customers whose first order is under £167 come back only 65.5% of the time against 84.9% for the largest first orders. And the calendar carries more weight than expected, with November alone delivering around 15% of annual revenue.

Those findings translate into three recommendations: a tiered key account programme protecting roughly £5.8m a year of Champion revenue, an automated second purchase email worth an estimated £55-60k, and peak season planning that starts in September rather than October.

## Business Problem

A business can look fine right up until the moment it isn't. Flat revenue is often read as stability, but it can just as easily be a shrinking customer base hidden behind bigger baskets, which is exactly the pattern this retailer shows. Before spending a pound on acquisition or promotions, leadership needs answers to four questions:

1. Who actually drives revenue, and how concentrated is it?
2. Are we keeping the customers we acquire, and can we spot future churn from the very first order?
3. When and where does demand concentrate, so stock, staffing and campaigns match reality?
4. Where is revenue leaking through returns and cancellations?

Answering them turns a flat topline from a mystery into a set of decisions.

## Data & Methodology

The dataset is [UCI Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii): 1,067,371 raw transactions from 1 Dec 2009 to 9 Dec 2011, cleaned down to 1,021,271 rows covering 5,852 identified customers.

The analysis is deliberately 100% SQL. Python appears only to load data and render charts, so every finding below traces back to a query the same way it would in a production environment.

1. **Cleaning pipeline** - a CTE chain that deduplicates, flags cancellations, strips out non-product stock codes, tags guest checkouts and derives revenue and date features.
2. **Revenue analysis** - growth broken into its moving parts (revenue, orders, order value), returns and cancellation rates, and monthly seasonality.
3. **RFM segmentation** - every customer scored on how recently they bought, how often and how much, the way a shopkeeper sizes up their regulars. `NTILE(5)` scoring mapped into seven segments with `CASE` logic, plus a concentration analysis inside the top segment.
4. **Cohort retention** - each month's new customers followed forward to see how quickly they fade.
5. **Acquisition quality** - first orders split into value quartiles (`FIRST_VALUE`, `NTILE(4)`) and tested against repeat rates, with a three month buffer so recent joiners don't bias the result.
6. **Geographic, product and time-of-day analysis** - revenue concentration by country and customer, the most sold and most returned products, and when orders actually arrive.

A representative query, testing whether a customer's first order predicts whether you ever see them again:

```sql
WITH sales AS (
    SELECT CustomerID, Invoice, Revenue, InvoiceDate,
           FIRST_VALUE(Invoice) OVER (
               PARTITION BY CustomerID
               ORDER BY InvoiceDate, Invoice) AS first_invoice
    FROM registered
    WHERE is_cancellation = 0 AND Quantity > 0
),
per_customer AS (
    SELECT CustomerID,
           COUNT(DISTINCT Invoice) AS orders,
           SUM(CASE WHEN Invoice = first_invoice THEN Revenue END) AS first_order_value
    FROM sales GROUP BY CustomerID
),
bucketed AS (
    SELECT *, NTILE(4) OVER (ORDER BY first_order_value) AS quartile
    FROM per_customer
)
SELECT quartile,
       ROUND(100.0 * SUM(CASE WHEN orders > 1 THEN 1 ELSE 0 END) / COUNT(*), 1)
           AS pct_returned
FROM bucketed
GROUP BY quartile ORDER BY quartile;
```

## Skills Used

- **SQL:** CTEs, window functions (`NTILE`, `LAG`, `ROW_NUMBER`, `FIRST_VALUE`, windowed `SUM`), `CASE` segmentation logic, `GROUP BY ROLLUP`, `QUANTILE_CONT`, date arithmetic
- **Python:** pandas, DuckDB, Jupyter
- **Visualisation:** Chart.js and Matplotlib, following *Storytelling with Data* principles (narrative headlines, direct labelling, colour used only for emphasis)
- **Version control:** Git/GitHub

## Results & Recommendations

### 1. The growth that isn't

Revenue grew 2.5% year on year. Orders fell 4%. The gap was covered by average order value rising 6.8% to £508, which means the topline is being carried by existing customers spending more, not by new ones arriving. New customer intake in year two ran well below year one, and the share of each month's buyers who were new fell steadily. Growth by bigger baskets has a ceiling, so the near term lever is retention and account expansion rather than acquisition volume. The findings below show where to pull it.

![Monthly revenue, orders and customers](docs/img/monthly_rev_order_cust.png)

### 2. Twenty five customers carry the business

The RFM model labels 1,275 customers as Champions, 22% of the identified base. Between them they generate £11.6m of the £17.1m attributable to identified customers, around £5.8m a year. And even inside this elite group the weight is not evenly spread. The median Champion is worth £3.9k against a mean of £9.1k, and just 25 accounts hold 34% of the segment's revenue. That skew is stable across both years, which makes it a structural feature of the business rather than a worsening trend.

Revenue this concentrated is usually managed by name. Here, none of it is. The recommendation is a tiered programme rather than a flat one: named account management for the top 25 accounts, standard VIP perks for the wider segment. Preventing even 5% attrition protects an estimated £290k a year.

![RFM customer segments](docs/img/rfm_segments.png)

### 3. The first order tells you the ending

How much someone spends on their first order turns out to be a reliable signal of whether they will ever return. Repeat rates climb at every step, from 65.5% for first orders under £167 to 84.9% at the top, and the sharpest cliff sits right at the bottom of the ladder. The window to act is short too, because only around 23% of customers are active in the month after their first purchase.

This points to a cheap experiment: an automated email to the roughly 1,300 lowest first-order customers within two weeks of purchase, nudging the second order that turns a one-time buyer into a regular. Closing even half the gap to the next quartile works out to about 67 extra repeat customers, an estimated £55-60k in revenue at the median customer value, for close to zero cost.

### 4. November pays for the year

November averages £1.44m in revenue, 1.8 times a typical month and roughly 15% of the year on its own. It is the peak in both trading years, driven by wholesale Christmas buying. A peak this predictable should be planned for rather than reacted to, so the recommendation is to begin inventory build-up and campaign scheduling in September, scaled by each product's own November uplift instead of a blanket percentage across the range.

![Average monthly revenue](docs/img/average_monthly_revenue_chart.png)

### 5. A trade business in a retail storefront

The clues are everywhere once you look. 86% of revenue is UK based, and the biggest international markets hang on one or two accounts each: EIRE's £623k comes from just 3 customers, and a single Dutch buyer accounts for £527k of that country's £550k. Orders arrive on weekdays, over 80% of revenue lands between 9:00 and 16:00, Thursday is the peak and Saturday is effectively zero. Add an average order of nearly £500 and the picture is clear. This is substantially a trade business, and it should be sold to like one.

France is the exception, with 93 customers and a genuinely broad base, which makes it the most diversifiable growth market. The recommendation: invest in French marketing and fulfilment, investigate Germany's elevated cancellation rate (27%) before spending there, land campaigns on Thursday mid-morning, and staff support and dispatch around the 9 to 4 window the customers actually keep.

## Next Steps

Every analysis raises the questions it cannot yet answer. With more time and data:

1. **A/B test the second purchase email** - measure open, click and 90-day repeat rates against a holdout group before rolling anything out.
2. **Build a SKU-level November seasonality index** in units rather than revenue, so purchasing can scale each product individually.
3. **Add margin data** - every estimate here is revenue based, and cost of goods would let the recommendations be ranked by profit impact instead.
4. **Extend RFM into customer lifetime value** to size the VIP and win-back cases more precisely.
5. **Known limitations** - around 22% of transactions are guest checkouts with no customer ID and sit outside the customer-level analysis; the window opens in Dec 2009, so some early "new" customers may be long-standing ones; and there is no marketing spend data to weigh acquisition cost against.

The topline said nothing was happening. The customer-level data said everything was: a business quietly narrowing to its best customers, and a set of levers for widening it back out.
