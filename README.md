# Online Retail Customer Analytics & Segmentation
### Cohort Analysis, RFM Segmentation, Product Value Analysis & Power BI Reporting

An end-to-end analysis of the **Online Retail II** transaction dataset — covering data cleaning and transaction classification, revenue methodology, cohort retention analysis, RFM customer segmentation, high-value product analysis, and a Power BI reporting layer.

**Key Highlights**

| Metric | Value |
|---|---|
| Customers analyzed | 5,942 |
| Total revenue (analysis period) | £16,289,991.29 |
| Revenue from High Value segment | 74.15% (from 21.88% of customers) |
| Average Month-1 cohort retention | ~25.0% |
| Strongest long-term cohort | December 2009 (~37.5% Month-1 retention) |
| High-Value product-level records | ~5,184 |

---

## Project Overview

This project analyzes transactional data from a UK-based online retailer to answer a practical business question: **which customers and products actually drive revenue, and how does customer value change over time?**

The analysis moves through five stages — data cleaning and transaction classification, revenue calculation, cohort-based retention analysis, RFM-based customer segmentation, and product-level analysis for the highest-value customer segment — before summarizing findings in a two-page Power BI report.

The emphasis throughout is on **analytical reasoning and business metric definition**, not on machine learning. Every classification and calculation in this project is deterministic and traceable back to raw transaction fields.

---

## Business Problem

The analysis is structured around five practical business questions:

**Customer Value**
- Who are the most valuable customers, and how concentrated is revenue across the customer base?
- Which customers most warrant retention investment?

**Customer Retention**
- What share of customers return after their first purchase?
- How does retention differ across acquisition cohorts?

**Customer Segmentation**
- Which customers are High Value, Loyal, Potential, or At Risk — and how do these groups differ in recency, frequency, and monetary contribution?

**Product Performance**
- Which products contribute most to High Value customer revenue?
- Does high unit volume actually correspond to high revenue?

**Revenue Quality**
- How should returns and cancellations factor into revenue reporting?
- What is the real difference between gross sales and net revenue?

---

## Dataset

The project uses the **Online Retail II** transactional dataset from a UK-based online retailer. Key fields used in the analysis:

| Field | Description |
|---|---|
| `Invoice` | Transaction/invoice number. Invoices prefixed with `C` indicate a cancellation. |
| `StockCode` | Product code. |
| `Description` | Product description. |
| `Quantity` | Units transacted (positive for sales, negative for returns/adjustments). |
| `InvoiceDate` | Date and time of the transaction. |
| `Price` | Unit price. |
| `Customer ID` | Unique customer identifier (not present for all transactions). |
| `Country` | Customer's country. |

The full transaction dataset is too large to store in this repository. The repository instead contains the analytical outputs (cohort tables, RFM segments, product-level output) and the Power BI supporting workbook.

**Dataset:** [Download Dataset](https://docs.google.com/spreadsheets/d/1TKdQ2ddkqfHPHctR_WSxmWcfD9UA6c7U/edit?usp=drive_link&ouid=109521645945143139035&rtpof=true&sd=true)

---

## End-to-End Workflow

```
Raw Online Retail Transaction Data
            ↓
Data Understanding & Cleaning
            ↓
Negative Transaction Investigation
            ↓
Return / Cancellation Classification
            ↓
Revenue & Customer Activity Metrics
            ↓
Cohort Retention Analysis
            ↓
RFM Customer Segmentation
            ↓
High-Value Customer Identification
            ↓
High-Value Product Analysis
            ↓
Power BI Dashboard
            ↓
Business Insights & Recommendations
```

---

## Data Cleaning & Transaction Handling

### Missing values

Rows with a missing `Customer ID` were excluded from customer-level analyses (cohort, RFM), since those transactions cannot be reliably attributed to a specific customer. Where `Description` was missing but `StockCode` had a valid description elsewhere in the data, the description was recovered using that `StockCode`.

### Duplicate records

Exact duplicate rows were identified and removed where appropriate.

### Data types

`InvoiceDate`, `Quantity`, `Price`, and `Customer ID` were checked and standardized before analysis.

### Negative quantities — classification, not deletion

Negative-quantity transactions were **investigated and classified**, not automatically deleted. Treating every negative quantity as a customer return would have distorted revenue analysis, since not all negative-quantity rows represent customer-initiated returns.

```
Quantity > 0
        → Sale

Quantity < 0  +  Invoice begins with "C"
        → Return / Cancellation

Quantity < 0  +  Invoice does not begin with "C"
        → Operational Negative Adjustment
```

Of the negative-quantity records identified:

| Type | Count |
|---|---|
| Total negative-quantity records | 22,950 |
| Associated with `C`-prefixed invoices (returns/cancellations) | 19,493 |
| Other negative records (operational adjustments) | 3,457 |

The operational adjustments include entries such as lost, damaged, check, mixed, short, and other non-sale, non-return corrections. Distinguishing these from genuine customer returns keeps the revenue and return-rate figures accurate.

---
**Cleaned Dataset:** [Download Dataset](https://drive.google.com/file/d/1sSyaaB05-Ub__SPn6G5xHIdOTdg56T88/view?usp=drive_link)  

## Revenue Methodology

```
LineAmount = Quantity × Price

Gross Sales   = Sum of positive sales transaction values
Return Value  = Absolute value of return/cancellation transaction values
Net Revenue   = Gross Sales − Return Value
```

Returns and cancellations are intentionally kept visible rather than netted away silently, so their financial impact can be reported and monitored on its own.

---

## Cohort Analysis

Customers were grouped by their **first purchase month** (their cohort), and tracked across subsequent months to measure repeat purchasing behavior.

| Field | Meaning |
|---|---|
| `PurchaseMonth` | Month in which a specific transaction occurred |
| `CohortMonth` | Customer's first-ever purchase month |
| `CohortIndex` | Number of months elapsed since the customer's first purchase |

**Findings:**

- Average Month-1 retention across cohorts is approximately **25.0%** — roughly three in four customers do not make a second purchase in the month immediately following their first.
- **December 2009** is the strongest long-term cohort in the available retention data, reaching approximately **37.5% Month-1 retention**.
- Retention does not decline perfectly monotonically for every cohort — some cohorts show a rebound in later months rather than a smooth decay curve.
- Newer cohorts cannot automatically be judged as stronger or weaker long-term performers, since they have a shorter observation window than older cohorts.

The gap between cohorts (e.g. December 2009 vs. the ~25% average) is worth further investigation into seasonality, acquisition channel, geography, or product mix — the cohort analysis surfaces *where* the difference exists, not definitively *why*.

---

## RFM Customer Segmentation

Each customer was scored on three dimensions and segmented based on the combined score.

**Recency** — how recently a customer purchased.
```
Recency = Analysis Date − Most Recent Purchase Date
```
Lower recency is better; scored in reverse (a smaller number of days yields a higher score).

**Frequency** — number of unique invoices/orders. Higher is better.

**Monetary** — total historical revenue generated by the customer.
```
Monetary = Sum of LineAmount
```
Higher is better.

Each customer's R, F, and M values were converted into relative 1–5 scores and combined into four segments: **High Value, Loyal, Potential, At Risk.**

### RFM Results

| Segment | Customers | Customer Share | Avg Recency | Avg Frequency | Avg Monetary | Total Revenue | Revenue Share |
|---|---:|---:|---:|---:|---:|---:|---:|
| High Value | 1,300 | 21.88% | 23.63 days | 21.88 | £9,291.49 | £12,078,942.30 | 74.15% |
| Loyal | 1,382 | 23.26% | 94.11 days | 6.57 | £1,951.08 | £2,696,387.43 | 16.55% |
| Potential | 1,459 | 24.55% | 199.75 days | 3.16 | £766.80 | £1,118,764.14 | 6.87% |
| At Risk | 1,801 | 30.31% | 415.07 days | 1.39 | £219.82 | £395,897.24 | 2.43% |
| **Total** | **5,942** | **100%** | — | — | — | **£16,289,991.29** | **100%** |

> **Key finding:** 21.88% of customers (High Value segment) generate 74.15% of total revenue.

This is a meaningful revenue-concentration signal — it means retention and service quality for a relatively small group of customers has an outsized effect on overall revenue.

### Segment Interpretation

**High Value** — 1,300 customers (21.88%), 74.15% of revenue. Very recent activity, high frequency, high monetary contribution. Strategically the most important segment to protect and retain.

**Loyal** — 1,382 customers (23.26%), 16.55% of revenue. Regular but lower-value purchasers than High Value — a natural upgrade path if frequency or basket size increases.

**Potential** — 1,459 customers (24.55%), 6.87% of revenue. Occasional purchasers; an opportunity segment for conversion into more regular customers.

**At Risk** — 1,801 customers (30.31%, the largest segment by count), but only 2.43% of revenue. Long average recency (415 days) and low frequency (1.39 orders). Important nuance: this segment should not be treated uniformly — an At Risk customer with a high historical monetary value is a different retention priority than one with a single low-value purchase.

---

## High-Value Product Analysis

After identifying the High Value customer segment, their transaction history was analyzed at the product level — revenue, units sold, and number of distinct High Value customers per product. The output contains approximately **5,184 product-level records**, with total revenue of **£12,078,942.30**, matching the High Value segment's revenue total.

**Leading products by revenue (High Value segment):**

1. REGENCY CAKESTAND 3 TIER
2. WHITE HANGING HEART T-LIGHT HOLDER
3. JUMBO BAG RED RETROSPOT
4. ASSORTED COLOUR BIRD ORNAMENT
5. PARTY BUNTING

### Product Revenue Concentration

```
Top 5 products    → 5.59% of High Value revenue
Top 10 products   → 8.01%
Top 20 products   → 11.48%
Top 50 products   → 19.31%
Top 100 products  → 28.67%
```

High Value customer revenue is spread across a broad product portfolio rather than concentrated in a small number of bestsellers — even the top 100 products account for under a third of total High Value revenue.

### Revenue vs. Units — volume is not the same as value

```
WORLD WAR 2 GLIDERS ASSTD DESIGNS
66,059 units  →  £15,736.37 revenue

REGENCY CAKESTAND 3 TIER
18,770 units  →  £213,890.50 revenue
```

A product with roughly 3.5x the unit volume generated less than 8% of the revenue of a lower-volume, higher-price product. Product performance should be evaluated using **revenue + units + customer reach together**, not unit volume alone.

---

## Power BI Dashboard

The repository includes a two-page Power BI report built on the prepared summary tables (KPI, monthly, weekly, customer activity, and country revenue tables).

**Page 1** — Gross Sales, Net Revenue, Returns, customer activity, monthly revenue trends, monthly orders/cancellations/returns, weekly revenue and activity trends.

**Page 2** — Monthly Net Revenue vs. Returns, monthly active customers, geographic revenue distribution.

> **Note:** The final dashboard pages do not use slicers or interactive filters — this is a static two-page summary report, not an interactive filtering tool.

### Dashboard KPIs

| KPI | Purpose |
|---|---|
| Gross Sales | Revenue before returns/cancellations |
| Net Revenue | Revenue after returns/cancellations |
| Returns | Monetary impact of returns |
| Customers | Unique customer activity |
| Return Rate | Relative level of returns |

Returns are kept visible as their own KPI rather than folded silently into Net Revenue, so their impact on the top line stays measurable.

---

## Key Business Insights

1. **Revenue concentration** — 21.88% of customers generate 74.15% of total revenue.
2. **Early-lifecycle drop-off** — Month-1 retention averages ~25%; the steepest customer loss happens right after the first purchase.
3. **Loyal is a real upgrade path** — a meaningful intermediate segment between High Value and lower-value groups (23.26% of customers, 16.55% of revenue).
4. **At Risk customers are numerous but low-value in aggregate** — the largest segment by count (30.31%) but only 2.43% of revenue.
5. **Product diversification** — High Value revenue is spread across a broad product portfolio, not dependent on a handful of bestsellers.
6. **Volume ≠ value** — high unit sales do not guarantee high revenue; price point matters as much as popularity.
7. **Returns materially affect revenue** — the gap between Gross Sales and Net Revenue is driven by a measurable volume of returns/cancellations, not noise.
8. **Cohort variation is real** — retention differs meaningfully by acquisition period (e.g. December 2009 vs. the ~25% average), warranting further investigation rather than being treated as random variance.

---

## Business Recommendations

These are recommendations *suggested by* the analysis, not experimentally validated interventions.

**Protect the High Value segment** — loyalty programs, personalized recommendations, and targeted retention outreach for the 21.88% of customers generating nearly three-quarters of revenue.

**Develop the Loyal segment** — cross-selling and personalized promotions aimed at increasing purchase frequency, since this segment already shows regular purchasing behavior.

**Prioritize At Risk customers selectively** — rather than spending retention budget uniformly across the whole segment, prioritize customers with high historical monetary value or frequency who have recently gone inactive.

**Use High Value product data for cross-selling** — products with both high revenue and broad reach among High Value customers are reasonable candidates for bundling and recommendation strategies.

---

## Analytical Considerations & Limitations

- The High-Value product-level output includes non-merchandise entries such as **Manual**, **Discount**, **CRUK Commission**, and other adjustment or returned-quantity records, some of which carry negative revenue. This means the product output is **not a purely clean merchandising table as-is** — before using it for final product ranking or merchandising decisions, adjustment/discount/commission/return records should be separated from genuine product sales.
- Cohort retention differences are reported descriptively; the analysis does not establish causal drivers (seasonality, geography, acquisition channel) behind why some cohorts retain better than others.
- The dashboard is a static two-page report without slicers, so cross-filtering between visuals is not available in the current version.

---

## Repository Structure

```
Retails_Analysis/
│
├── Analysis.ipynb
├── Project Description.docx
│
├── Handling Returns and Negatives/
│   ├── Transaction_types.csv
│   └── handling_retunr-negatives.md
│
├── Cohort analysis/
│   ├── Cohort_analysis.md
│   └── Retention_matrix.csv
│
├── RFM Analysis/
│   ├── CustomerSegment_RFM_analysis.csv
│   ├── High_value_products.csv
│   └── RFM_CustomerSegmrnt_HighVlueProduct_Analysis.md
│
└── Revenue and Customer Activity Dashboard/
    ├── DashBoard.pbix
    ├── DashBoard.pdf
    ├── Dashboard_Analysis_Report.pdf
    └── online_retail_powerbi_data.xlsx

README.md
.gitattributes
```

---

## Project Files & Deliverables

| File | Description |
|---|---|
| [Analysis.ipynb](Retails_Analysis/Analysis.ipynb) | Main Python analysis notebook |
| [Project Description.docx](Retails_Analysis/Project%20Description.docx) | Project write-up |
| [Transaction_types.csv](Retails_Analysis/Handling%20Returns%20and%20Negatives/Transaction_types.csv) | Sale / Return / Operational Adjustment classification |
| [handling_retunr-negatives.md](Retails_Analysis/Handling%20Returns%20and%20Negatives/handling_retunr-negatives.md) | Notes on negative-transaction handling |
| [Cohort_analysis.md](Retails_Analysis/Cohort%20analysis/Cohort_analysis.md) | Cohort methodology and findings |
| [Retention_matrix.csv](Retails_Analysis/Cohort%20analysis/Retention_matrix.csv) | Cohort retention matrix |
| [CustomerSegment_RFM_analysis.csv](Retails_Analysis/RFM%20Analysis/CustomerSegment_RFM_analysis.csv) | RFM segment summary |
| [High_value_products.csv](Retails_Analysis/RFM%20Analysis/High_value_products.csv) | Product-level output for High Value customers |
| [RFM_CustomerSegmrnt_HighVlueProduct_Analysis.md](Retails_Analysis/RFM%20Analysis/RFM_CustomerSegmrnt_HighVlueProduct_Analysis.md) | RFM and product analysis write-up |
| [DashBoard.pbix](Retails_Analysis/Revenue%20and%20Customer%20Activity%20Dashboard/DashBoard.pbix) | Power BI source file |
| [DashBoard.pdf](Retails_Analysis/Revenue%20and%20Customer%20Activity%20Dashboard/DashBoard.pdf) | Dashboard export |
| [Dashboard_Analysis_Report.pdf](Retails_Analysis/Revenue%20and%20Customer%20Activity%20Dashboard/Dashboard_Analysis_Report.pdf) | Dashboard analysis write-up |
| [online_retail_powerbi_data.xlsx](Retails_Analysis/Revenue%20and%20Customer%20Activity%20Dashboard/online_retail_powerbi_data.xlsx) | Supporting Power BI tables |

---

## Tools & Technologies

- **Python** — data cleaning, transaction classification, cohort and RFM calculations
- **Pandas** — data manipulation and aggregation
- **Jupyter Notebook** — analysis environment
- **Excel / WPS Office** — supporting tables and Power BI data prep
- **Power BI** — dashboard reporting
- **GitHub / Markdown** — version control and documentation

---

## Reproducibility

The full transaction dataset is not stored in this repository due to its size and must be obtained separately — see the [Dataset](#dataset) section above for the placeholder download link.

The repository already contains the analytical outputs (cohort retention matrix, RFM segment summary, high-value product CSV) and the Power BI supporting workbook, so the findings can be reviewed without re-running the notebook. Full reproduction of `Analysis.ipynb` from raw data requires the external dataset; there is no `requirements.txt` in this repository at present.

---

## Conclusion

This project demonstrates a complete analytics workflow on real transactional data: classifying ambiguous transaction types instead of discarding them, defining a defensible revenue methodology, segmenting customers by actual behavior rather than assumption, and translating the results into a two-page business-facing report. The strongest finding — that a fifth of customers generate three-quarters of revenue — is the kind of insight that directly informs where a retail business should focus its retention effort.
