
# Cohort Analysis — Business Insights

## 1. Early Repeat Purchase Behavior

The retention table shows that repeat purchasing drops sharply after the initial purchase month.

* Across cohorts with complete Month-1 data, average Month-1 retention is approximately **25.0%**.
* This means roughly **75% of customers do not make another purchase in the following month**.
* The strongest Month-1 retention is observed in the **December 2009 cohort at 37.5%**.
* Several later cohorts also show strong early retention, particularly **September 2011 (34.2%)**, **October 2011 (33.5%)**, and **August 2011 (33.0%)**.
* This indicates that repeat purchasing exists, but the business loses a large proportion of newly acquired customers after their initial purchase.

## 2. Retention Over Time

Retention generally declines as the cohort ages, although the decline is not perfectly monotonic.

* Average retention across cohorts falls from approximately **25.0% in Month 1** to **22.2% in Month 3**, **18.5% in Month 6**, and **18.3% in Month 12**.
* This suggests that the major customer drop-off occurs early, after which a smaller but relatively persistent group of repeat customers remains active.
* The retention pattern therefore resembles a **large initial drop followed by a smaller retained customer base**.

## 3. Strongest Long-Term Cohort

The **December 2009 cohort** is clearly the strongest long-term performer in the available retention table.

* Month 1 retention: **37.5%**
* Month 3 retention: **42.8%**
* Month 6 retention: **39.0%**
* Month 12 retention: **37.0%**
* Month 24 retention: **20.9%**

This cohort maintains substantially stronger long-term retention than most other cohorts.

### Business implication

The December 2009 customer group appears to contain a particularly valuable or loyal customer base. The business should investigate what was different about customers acquired during this period, including product mix, acquisition source, seasonality, customer geography, and purchasing behavior.

## 4. Newer Cohort Performance

The more recent cohorts show strong **initial repeat-purchase activity in some cases**, but most do not yet have enough historical data to evaluate long-term retention.

For example:

* September 2011 reaches **34.2% in Month 1** and **39.0% in Month 2**.
* August 2011 reaches **33.0% in Month 1** and **31.1% in Month 2**.
* July 2011 reaches **23.5% in Month 1**, **30.4% in Month 2**, and **29.4% in Month 3**.

This suggests that some newer cohorts may be performing well in the early customer lifecycle.

However, they should **not be declared better long-term cohorts** yet because they have not been observed for as many months as the older cohorts.

## 5. Significant Cohort Variability

Retention differs considerably between cohorts.

For Month 1:

* Highest observed: **37.5%**
* Lowest observed: **11.7%**
* Median: approximately **23.8%**

This is a substantial difference between cohorts.

### Business implication

Customer retention is not constant across acquisition periods. Something about the timing or composition of customer acquisition may be influencing customer quality.

This warrants further diagnostic analysis by:

* Country
* Product/category
* Customer value
* Purchase behavior
* Acquisition period
* Seasonality

## 6. Long-Term Retention Is Not Necessarily Monotonic

The retention percentages do not always decrease every month.

For example, the December 2009 cohort moves:

```text
Month 1   → 37.5%
Month 2   → 34.3%
Month 3   → 42.8%
Month 10  → 43.3%
Month 11  → 49.6%
Month 12  → 37.0%
Month 23  → 40.9%
Month 24  → 20.9%
```

This indicates that customers can **return after periods of inactivity** rather than following a simple continuously declining retention pattern.

### Business implication

The business may have customers with **irregular or seasonal purchasing cycles**. Therefore, interpreting every temporary decline as permanent churn could be misleading.

## 7. Seasonal Effects May Be Present

Several cohorts show temporary increases in retention after earlier declines.

For example, the December 2009 cohort experiences strong rebounds around Months 10–11, while other cohorts also show increases after several lower-retention periods.

This suggests that customer purchasing behavior may contain **seasonal or event-driven patterns**.

### Business implication

Retention should therefore be interpreted alongside calendar-based sales trends before concluding that customers have permanently disengaged.

## 8. Important Cohort Comparison

The data suggests two different customer-retention patterns:

### Strong early + strong long-term cohorts

These cohorts retain a meaningful proportion of customers over many months.

**Example:** December 2009.

### Strong early + limited observation cohorts

Some newer cohorts show strong Month-1 and Month-2 retention but do not yet have enough history to determine long-term performance.

**Example:** September 2011.

This distinction is important because **older cohorts have had more opportunity to demonstrate long-term retention**, while newer cohorts are naturally truncated.

## 9. Business Questions Raised by the Cohort Results

# RFM, Customer Segment & High-Value Product Analysis

## 1. Objective

The objective of this analysis was to identify the most valuable customers using **RFM analysis** and then determine which products contribute most to the revenue generated by those high-value customers.

The analysis moves through the following chain:

```text
Transaction Data
        ↓
Customer-Level RFM Metrics
        ↓
RFM Scoring
        ↓
Customer Segmentation
        ↓
Identify High-Value Customers
        ↓
Link High-Value Customers to Products
        ↓
Product Revenue Analysis
        ↓
Business Insights
```

---

# 2. Data Used

Two analytical outputs were used:

### `CustomerSegment_RFM_analysis.csv`

Contains the customer-level segment summary:

* `CustomerSegment`
* `Customers`
* `AvgRecency`
* `AvgFrequency`
* `AvgMonetary`
* `TotalRevenue`

### `High_value_products.csv`

Contains product-level performance for customers classified as **High Value**:

* `Description`
* `Revenue`
* `units`
* `Customers`

---

# 3. RFM Methodology

RFM stands for:

```text
R → Recency
F → Frequency
M → Monetary Value
```

## 3.1 Recency

Measures how recently a customer made a purchase.

```text
Recency = Analysis Date - Customer's Most Recent Purchase Date
```

Lower values indicate more recent purchasing activity.

## 3.2 Frequency

Measures how many unique orders a customer made.

```text
Frequency = Number of Unique Invoices
```

Higher values indicate more frequent purchasing.

## 3.3 Monetary Value

Measures the total historical revenue generated by a customer.

```text
Monetary Value = Sum of LineAmount
```

where:

```text
LineAmount = Quantity × Price
```

Higher values indicate greater historical revenue contribution.

---

# 4. RFM Scoring

Customers were converted from raw RFM values into relative scores from **1 to 5**.

* Recency was scored in reverse because lower recency is better.
* Frequency was scored normally because higher frequency is better.
* Monetary value was scored normally because higher monetary value is better.

The resulting scores were combined into an overall RFM score and used to classify customers into segments.

The analysis produced four segments:

```text
High Value
Loyal
Potential
At Risk
```

---

# 5. Customer Segment Output

| Segment         |       Customers | Customer Share | Avg Recency | Avg Frequency | Avg Monetary |             Total Revenue |  Revenue Share |
| --------------- | --------------: | -------------: | ----------: | ------------: | -----------: | ------------------------: | -------------: |
| High Value      |           1,300 |         21.88% |  23.63 days |         21.88 |   £9,291.49 |           £12,078,942.30 |         74.15% |
| Loyal           |           1,382 |         23.26% |  94.11 days |          6.57 |   £1,951.08 |            £2,696,387.43 |         16.55% |
| Potential       |           1,459 |         24.55% | 199.75 days |          3.16 |     £766.80 |            £1,118,764.14 |          6.87% |
| At Risk         |           1,801 |         30.31% | 415.07 days |          1.39 |     £219.82 |              £395,897.24 |          2.43% |
| **Total** | **5,942** | **100%** |          — |            — |           — | **£16,289,991.29** | **100%** |

---

# 6. Customer Segment Analysis

## 6.1 High Value Customers

The High Value segment contains **1,300 customers**, representing only **21.88% of customers**.

However, these customers generated:

```text
£12.08 million
```

which represents approximately:

```text
74.15% of total revenue
```

Their average characteristics are:

```text
Average Recency   = 23.63 days
Average Frequency = 21.88 orders
Average Monetary  = £9,291.49
```

### Business Insight

The business is **extremely dependent on a relatively small high-value customer group**.

Approximately **1 in 5 customers generates nearly three-quarters of the revenue**.

This is the strongest finding from the RFM analysis.

---

## 6.2 Loyal Customers

The Loyal segment contains **1,382 customers**, representing **23.26% of customers**.

They generate:

```text
£2.70 million
```

or approximately:

```text
16.55% of total revenue
```

Their average behavior is:

```text
Recency   = 94.11 days
Frequency = 6.57 orders
Monetary  = £1,951.08
```

### Business Insight

Loyal customers are materially more valuable than Potential and At Risk customers, but their average recency and purchase frequency are substantially below the High Value segment.

This group is an important **upgrade opportunity**: increasing purchasing frequency or reducing the time between purchases could potentially move some Loyal customers toward the High Value segment.

---

## 6.3 Potential Customers

The Potential segment contains **1,459 customers**, approximately **24.55% of the customer base**.

They contribute:

```text
£1.12 million
```

or approximately:

```text
6.87% of total revenue
```

Their average behavior:

```text
Recency   = 199.75 days
Frequency = 3.16 orders
Monetary  = £766.80
```

### Business Insight

This group already demonstrates more purchasing activity than the At Risk group but is significantly less engaged than Loyal customers.

The major opportunity is **conversion from occasional buyers into regular customers**.

---

## 6.4 At Risk Customers

The At Risk segment is the largest customer group:

```text
1,801 customers
```

representing:

```text
30.31% of customers
```

Yet it contributes only:

```text
£395,897.24
```

or:

```text
2.43% of revenue
```

Its average behavior is:

```text
Recency   = 415.07 days
Frequency = 1.39 orders
Monetary  = £219.82
```

### Business Insight

This is a large customer population with very low current commercial value.

The average customer in this group has purchased only about **1–2 times** and has not purchased for approximately **415 days**.

The business should therefore distinguish between:

```text
At Risk High-Value Customers
```

and:

```text
At Risk Low-Value Customers
```

because the cost of reactivating every At Risk customer is unlikely to be justified equally.

---

# 7. RFM Segment Comparison

The progression is very clear:

```text
High Value
↓
23.63 days recency
21.88 orders
£9,291 average monetary value

Loyal
↓
94.11 days recency
6.57 orders
£1,951 average monetary value

Potential
↓
199.75 days recency
3.16 orders
£767 average monetary value

At Risk
↓
415.07 days recency
1.39 orders
£220 average monetary value
```

The three RFM dimensions move in the expected direction.

As customers become less recent, they also tend to purchase less frequently and generate less historical revenue.

### Business Insight

The segmentation is not merely producing arbitrary labels. The resulting groups demonstrate a **clear behavioral gradient from high engagement to low engagement**.

---

# 8. High-Value Product Analysis

The `High_value_products.csv` output contains **5,184 product-level records** associated with the High Value customer segment.

The total product revenue is:

```text
£12,078,942.30
```

which exactly matches the revenue attributed to the High Value segment.

This confirms that the product-level analysis represents the purchasing activity of the identified High Value customers.

---

# 9. Top Products by Revenue

The leading products by revenue among High Value customers are:

| Rank | Product                            |      Revenue |
| ---: | ---------------------------------- | -----------: |
|    1 | REGENCY CAKESTAND 3 TIER           | £213,890.50 |
|    2 | WHITE HANGING HEART T-LIGHT HOLDER | £173,848.31 |
|    3 | JUMBO BAG RED RETROSPOT            | £112,177.17 |
|    4 | ASSORTED COLOUR BIRD ORNAMENT      |  £98,677.82 |
|    5 | PARTY BUNTING                      |  £76,889.48 |
|    6 | POSTAGE                            |  £71,394.31 |
|    7 | CHILLI LIGHTS                      |  £58,276.00 |
|    8 | BLACK RECORD COVER FRAME           |  £55,143.92 |
|    9 | JUMBO BAG STRAWBERRY               |  £53,863.23 |
|   10 | PAPER CHAIN KIT 50'S CHRISTMAS     |  £53,280.38 |

---

# 10. Product Revenue Concentration

Although there are **5,184 product records**, revenue is spread across a very large product catalogue.

The top:

```text
5 products  → 5.59% of High Value revenue
10 products → 8.01%
20 products → 11.48%
50 products → 19.31%
100 products → 28.67%
```

### Business Insight

High-value customer revenue is **not dependent on a tiny handful of products**.

The largest products are important, but even the top 100 products account for less than one-third of High Value revenue.

This suggests a relatively broad product portfolio contributes to high-value customer revenue.

---

# 11. Products with Broad High-Value Customer Reach

Some products are purchased by a particularly large number of High Value customers.

Examples include:

| Product                            | High-Value Customers |
| ---------------------------------- | -------------------: |
| WHITE HANGING HEART T-LIGHT HOLDER |                  568 |
| REGENCY CAKESTAND 3 TIER           |                  562 |
| JUMBO BAG RED RETROSPOT            |                  468 |
| BAKING SET 9 PIECE RETROSPOT       |                  449 |
| PAPER CHAIN KIT 50'S CHRISTMAS     |                  427 |
| ASSORTED COLOUR BIRD ORNAMENT      |                  425 |
| PARTY BUNTING                      |                  423 |

### Business Insight

These products are not merely generating revenue; they have **broad penetration across the High Value customer base**.

They may therefore represent strong candidates for:

* Cross-selling
* Bundling
* Repeat-purchase campaigns
* Customer recommendations

---

# 12. Revenue vs. Volume

The product analysis shows that the products with the highest unit volume are not always the products generating the highest revenue.

For example:

```text
WORLD WAR 2 GLIDERS ASSTD DESIGNS
66,059 units
£15,736.37 revenue
```

while:

```text
REGENCY CAKESTAND 3 TIER
18,770 units
£213,890.50 revenue
```

### Business Insight

**Units sold alone are not sufficient for identifying valuable products.**

A product can have very high volume but relatively low revenue per unit, while another product can have lower volume but a much greater revenue contribution.

Therefore, product decisions should consider at least:

```text
Revenue
Units
Customers
```

rather than relying exclusively on sales volume.

---

# 13. Important Data Observation in High-Value Products

The product output contains **negative-revenue records**, including:

* `Manual`
* `Discount`
* `CRUK Commission`
* `Adjustment by Peter on 24/05/2010 1`
* Several products with returned quantities

For example:

```text
Manual                       -£30,917.40
Discount                     -£12,680.57
CRUK Commission               -£7,933.43
```

There are **30 products with negative revenue** and **12 with zero revenue** in the high-value product output.

### Business Insight

This indicates that the product-level output contains more than normal product sales; it also captures **adjustments, discounts, commissions, and return-related activity**.

These records should be separated from normal product performance before using the table for product ranking or merchandising decisions.

Otherwise, "product revenue" may mix:

```text
Actual Product Sales
+
Returns
+
Discounts
+
Operational/Financial Adjustments
```

---

# 14. Overall Customer Value Structure

The customer base can be viewed as:

```text
30.31% At Risk
        ↓
24.55% Potential
        ↓
23.26% Loyal
        ↓
21.88% High Value
```

But revenue contribution is dramatically different:

```text
At Risk      →  2.43%
Potential    →  6.87%
Loyal        → 16.55%
High Value   → 74.15%
```

### Business Insight

Customer count is **not proportional to customer value**.

The largest customer segment, At Risk, contributes the smallest meaningful share of revenue, while the High Value segment contributes by far the largest share.

This is a classic example of why customer analysis should use **value-weighted metrics rather than customer counts alone**.

---

# 15. Key Business Insights

## Insight 1 — Revenue is heavily concentrated in High Value customers

Only **21.88% of customers generate 74.15% of revenue**.

This makes High Value customers the most financially important group in the dataset.

---

## Insight 2 — High Value customers are substantially more engaged

Compared with At Risk customers:

```text
Recency:
23.63 vs 415.07 days

Frequency:
21.88 vs 1.39 orders

Average Monetary Value:
£9,291 vs £220
```

The difference is large across all three dimensions.

---

## Insight 3 — The Loyal segment is the strongest upgrade pool

Loyal customers already purchase:

```text
6.57 times on average
```

and contribute:

```text
£2.70 million
```

in total revenue.

They are far more commercially valuable than Potential and At Risk customers, making them a logical group for programs designed to increase purchase frequency and monetary value.

---

## Insight 4 — The At Risk segment is large but commercially weak

At Risk customers represent **30.31% of the customer base** but only **2.43% of revenue**.

This means a blanket reactivation strategy for every At Risk customer may be inefficient.

A better strategy would prioritize customers who were historically valuable before becoming inactive.

---

## Insight 5 — High-value revenue is diversified across many products

The top 100 products contribute only **28.67%** of High Value revenue.

Therefore, the business is not dependent on a tiny product shortlist for its high-value customer revenue.

---

## Insight 6 — Several products have both high revenue and broad customer penetration

Products such as:

* `WHITE HANGING HEART T-LIGHT HOLDER`
* `REGENCY CAKESTAND 3 TIER`
* `JUMBO BAG RED RETROSPOT`
* `PAPER CHAIN KIT 50'S CHRISTMAS`
* `ASSORTED COLOUR BIRD ORNAMENT`

are notable because they combine substantial revenue with purchases from hundreds of High Value customers.

These products are stronger candidates for **cross-selling and bundling analysis** than products with high revenue but very narrow customer reach.

---

## Insight 7 — High unit volume does not necessarily mean high financial value

Some products sell tens of thousands of units but generate much less revenue than products with fewer units sold.

This demonstrates the need to analyze:

```text
Revenue + Units + Customer Reach
```

together.

---

## Insight 8 — Product performance needs a clean sales-only view

Negative product revenues reveal that the High Value product output contains financial and operational adjustment records.

Before final product recommendations, these should be classified separately so that genuine product sales are not confused with:

```text
returns
discounts
commissions
manual adjustments
```

---

# 16. Recommended Business Actions

### Protect High Value Customers

Because High Value customers generate **74.15% of revenue**, retention of this group should receive the highest priority.

Possible actions:

```text
Targeted loyalty programs
Personalized recommendations
Early access / exclusive offers
High-value customer retention campaigns
```

### Convert Loyal Customers

The Loyal segment is the clearest intermediate opportunity.

Focus on:

```text
Increasing purchase frequency
Cross-selling
Product recommendations
Personalized promotions
```

### Prioritize At-Risk Reactivation by Historical Value

Do not treat every At Risk customer equally.

Prioritize:

```text
High historical monetary value
+
Previously high frequency
+
Now high recency
```

This creates a more targeted win-back strategy.

### Use High-Value Products for Cross-Selling

Products with both:

```text
High revenue
+
High High-Value customer reach
```

are strong candidates for recommendation and bundle strategies.

---

# 17. Final Analytical Conclusion

The RFM analysis reveals a **highly concentrated customer-value structure**.

A relatively small group of **1,300 High Value customers (21.88%) generates 74.15% of total revenue**, supported by very recent purchasing behavior, high order frequency, and high historical monetary contribution.

The next most valuable group is the **Loyal segment**, which represents an opportunity for progression into the High Value segment.

At the other end, **30.31% of customers are classified as At Risk but contribute only 2.43% of revenue**, indicating that reactivation efforts should be prioritized using historical customer value rather than customer count alone.

The High Value product analysis shows that revenue is distributed across a broad product portfolio, while several products combine high revenue with high penetration among valuable customers. These products are particularly relevant for cross-selling, bundling, and retention strategies.

Overall, the analysis supports a business strategy centered on:

```text
Protect High-Value Customers
            ↓
Upgrade Loyal Customers
            ↓
Target High-Value At-Risk Customers
            ↓
Use Proven High-Value Products
for Cross-Selling & Retention
```

The retention table indicates several questions that should be investigated next:

1. Why did the December 2009 cohort retain customers much better than most later cohorts?
2. What changed between the strongest and weakest acquisition periods?
3. Are high-retention cohorts concentrated in specific countries?
4. Are particular products responsible for repeat purchasing?
5. Do high-retention customers also generate higher revenue?
6. Are the retention rebounds caused by seasonal purchasing?
7. Are customers returning to purchase the same products or different products?
8. Does a customer's first-order value predict later retention?

## 10. Analytical Limitation

This pivot table is a **retention percentage matrix**. It directly supports conclusions about:

* Cohort retention
* Repeat activity over time
* Customer drop-off
* Cohort-to-cohort comparison
* Long-term retention patterns

It does **not by itself provide**:

* Number of purchases per customer
* Number of active months per customer
* Average orders per customer
* Exact repeat-purchase rate at the individual-customer level
* Time between purchases
* Revenue contribution

Those require additional customer-level calculations from `cohort_data`.

## 11. Overall Business Insight

> **The strongest finding is that customer retention varies substantially by acquisition cohort, with a major drop after the first month but a smaller group of customers continuing to purchase over the long term. The December 2009 cohort is an exceptional long-term performer, while several newer cohorts show promising early retention but lack sufficient history for long-term comparison.**

The next analytical step should therefore be to identify **what characteristics distinguish high-retention customers and cohorts from low-retention ones**, rather than simply reporting the retention percentages.
