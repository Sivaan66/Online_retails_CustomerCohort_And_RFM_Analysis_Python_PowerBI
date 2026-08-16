
# Handling Negative Quantities and Returns

## 1. Objective

Negative `Quantity` values in the Online Retail II dataset should not be treated as ordinary data-entry errors and deleted automatically. They represent different business events, including cancelled transactions, returns, and operational stock adjustments.

The objective of this step is to distinguish these cases, preserve the original transaction information, and ensure that sales and return metrics are calculated correctly.

---

## 2. Understanding Negative Quantities

The dataset contains two broad types of negative-quantity records:

1. **Cancellation/return transactions** — generally identified by an `Invoice` beginning with `C`.
2. **Other negative adjustments** — negative quantities where the invoice does not begin with `C`. These records frequently have a `Price` of `0`, missing `Customer ID`, and descriptions such as `lost`, `damages`, `check`, `mixed`, or `sold as ...`.

Therefore, a negative quantity alone is not sufficient to classify a record as a customer return.

### Dataset evidence

| Dataset period  |    Negative rows | `C`-invoice negatives | Other negatives |
| --------------- | ---------------: | ----------------------: | --------------: |
| 2009–2010      |           12,326 |                  10,205 |           2,121 |
| 2010–2011      |           10,624 |                   9,288 |           1,336 |
| **Total** | **22,950** |        **19,493** | **3,457** |

The non-`C` negative records have zero prices in this dataset, meaning that treating all negative quantities as monetary returns would produce misleading revenue calculations.

---

## 3. Classification Rule

Create a transaction classification field based on `Quantity` and `Invoice`.

### Recommended logic

```text
IF Quantity > 0
    → Sale

IF Quantity < 0 AND Invoice starts with "C"
    → Return / Cancellation

IF Quantity < 0 AND Invoice does not start with "C"
    → Operational Negative Adjustment
```

This classification should be created as a new field rather than overwriting the original `Quantity` or `Invoice` columns.

For example:

```text
TransactionType
----------------------------
Sale
Return/Cancellation
Operational Adjustment
```

---

## 4. Why Negative Returns Should Not Be Deleted

Deleting all negative quantities would remove legitimate business information.

Return/cancellation records provide information about:

* returned units
* cancelled sales
* return value
* products with high return activity
* customers generating returns
* countries with higher return activity
* net sales after returns

Therefore, negative return transactions should remain in the analytical dataset.

The correct approach is to **classify and handle them separately**, not remove them.

---

## 5. Handling Returns in Sales Calculations

For return/cancellation records, the negative quantity should normally be retained.

For example:

```text
Quantity = -5
Price = £10
Transaction Value = -5 × £10
                   = -£50
```

The negative value represents a reduction in sales revenue.

A standard line-value calculation is:

```text
LineAmount = Quantity × Price
```

This allows sales, returns, and net revenue to be calculated consistently.

### Recommended metrics

**Gross Sales**

```text
Sum of LineAmount where TransactionType = Sale
```

**Return Value**

```text
ABS(Sum of LineAmount where TransactionType = Return/Cancellation)
```

**Net Sales**

```text
Gross Sales - Return Value
```

Alternatively, when the signed transaction values are retained:

```text
Net Sales = Sum of LineAmount
```

Both approaches are valid, provided the methodology is clearly documented.

---

## 6. Handling Operational Negative Adjustments

Non-`C` negative quantities should not automatically be interpreted as customer returns.

Examples found in the dataset include descriptions such as:

```text
lost
damages
check
mixed
sold as ...
short
```

Many of these records have:

```text
Quantity < 0
Price = 0
Customer ID = missing
```

This strongly suggests that these records represent operational or inventory-related adjustments rather than normal customer sales or monetary returns.

### Recommended treatment

Keep these records in the raw and cleaned datasets, but classify them separately as:

```text
Operational Adjustment
```

They should generally be excluded from:

* Gross Sales
* Customer Revenue
* Average Order Value
* Revenue by Country
* Product Sales Revenue

unless the specific business question requires inventory-adjustment analysis.

They may still be useful for a separate operational analysis involving:

* damaged products
* lost stock
* inventory discrepancies
* warehouse issues
* stock corrections

---

## 7. Do Not Convert All Negative Quantities to Positive Values

A common but incorrect cleaning approach is:

```text
ABS(Quantity)
```

applied to every negative quantity.

This destroys the meaning of the transaction.

For example:

```text
Sale:              +10 units
Return:             -3 units
```

Changing `-3` to `+3` would make the return appear to be an additional sale.

Negative values should therefore remain negative for return/cancellation records.

---

## 8. Recommended Data Model

The original fields should be preserved, while additional analytical fields are created.

### Suggested fields

```text
Quantity
Price
LineAmount
TransactionType
IsReturn
IsOperationalAdjustment
```

Example:

| Quantity | Price | LineAmount | TransactionType        | IsReturn | IsOperationalAdjustment |
| -------: | ----: | ---------: | ---------------------- | -------- | ----------------------- |
|       10 |  5.00 |      50.00 | Sale                   | 0        | 0                       |
|       -2 |  5.00 |     -10.00 | Return/Cancellation    | 1        | 0                       |
|      -20 |  0.00 |       0.00 | Operational Adjustment | 0        | 1                       |

This structure makes the dataset much safer for later Excel, Power BI, and analytical calculations.

---

## 9. Important Analytical Distinction

The dataset should distinguish between:

```text
Gross Sales
        ↓
Customer Returns/Cancellations
        ↓
Net Sales
```

and separately:

```text
Operational Adjustments
        ↓
Inventory / Operational Analysis
```

Operational adjustments should not be mixed with customer returns because they answer different business questions.

A business analyst should therefore avoid reporting a single metric such as:

> "Total negative quantity = returns"

because that would incorrectly classify operational adjustments as customer returns.

---

## 10. Recommended Cleaning Policy

The final cleaning policy should be:

```text
1. Preserve the original Quantity values.
2. Identify negative quantities.
3. Identify invoices beginning with "C".
4. Classify negative C-invoice records as Return/Cancellation.
5. Classify remaining negative records as Operational Adjustment.
6. Keep both categories in the cleaned dataset.
7. Use only Sale records for gross-sales KPIs.
8. Use Return/Cancellation records for return analysis and net-sales calculations.
9. Exclude Operational Adjustments from sales KPIs unless specifically required.
10. Do not convert negative quantities to positive values.
11. Document the classification logic so that all downstream analysis uses the same rules.
```

## 11. Validation Checks

After classification, perform the following checks:

```text
Total rows
=
Sale rows
+
Return/Cancellation rows
+
Operational Adjustment rows
```

Also verify:

```text
Return/Cancellation → Quantity < 0
Operational Adjustment → Quantity < 0
Sale → Quantity > 0
```

The dataset contains no zero-quantity records in either year, so a separate zero-quantity category is not required for the current cleaning process.

Finally, compare:

```text
Gross Sales
Return Value
Net Sales
Operational Adjustment Count
```

before proceeding to EDA and dashboard development.

---

## 12. Final Principle

**Do not clean away negative values simply because they look unusual.**

Negative quantities are meaningful business signals. The correct cleaning strategy is to **identify what the negative transaction represents, classify it, preserve the original record, and control how it is used in each KPI**.

This prevents returns, cancellations, and operational stock adjustments from being incorrectly interpreted as sales or lost as part of the cleaning process.
