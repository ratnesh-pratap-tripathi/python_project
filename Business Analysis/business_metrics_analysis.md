# Business Metrics Analysis

**Date:** October 8, 2026  
**Dataset:** `business_metrics_dataset.xlsx`  
**Tools:** Python, Pandas, Matplotlib

---

## 1. Project Overview

This project analyzes business performance using financial,
operational, and customer-related metrics.

The dataset contains 24 months of business data, covering
January 2025 through December 2026.

### Objectives

- Analyze business profitability.
- Calculate Return on Investment (ROI).
- Evaluate cash runway.
- Analyze Average Order Value (AOV).
- Measure Customer Acquisition Cost (CAC).
- Calculate EBITDA.
- Visualize business performance using Python.

---

## 2. Import Libraries

```python
import pandas as pd
import matplotlib.pyplot as plt
```

## 3. Load Dataset

```python
df = pd.read_excel('business_metrics_dataset.xlsx')
```

### Preview Dataset

```python
df.head()
```

The dataset contains 24 observations and 21 columns.

### Key Columns

- Month
- Amount Invested
- Net Profit
- Total Revenue
- Number of Orders
- Marketing & Sales Costs
- New Customers
- Customers at Start
- Customers Lost
- Customers at End
- Average Monthly Revenue per Customer
- Available Cash
- Monthly Cash Outflows
- Monthly Cash Inflows
- Net Income
- Interest Expense
- Income Taxes
- Depreciation
- Amortization
- Gross Operating Costs

---

## 4. Exploratory Data Analysis

Generate descriptive statistics:

```python
df.describe().round(2)
```

### Summary Statistics

| Metric | Mean | Minimum | Maximum |
|--------|------|---------|---------|
| Net Profit | 406,258.14 | 134,050.30 | 807,522.44 |
| Total Revenue | 1,277,854.57 | 451,116.53 | 2,535,278.64 |
| Number of Orders | 1,088.46 | 667 | 1,502 |
| Marketing & Sales Costs | 125,129.94 | 85,772.39 | 168,543.61 |
| New Customers | 103.21 | 51 | 151 |
| Customers at End | 1,223.88 | 461 | 2,161 |
| Available Cash | 5,483,816.81 | 1,719,050.30 | 11,590,195.37 |
| Net Income | 406,258.14 | 134,050.30 | 807,522.44 |
| Gross Operating Costs | 771,565.67 | 279,354.49 | 1,536,460.42 |

### Observations

- Revenue increases significantly across the dataset.
- Net profit shows strong growth.
- The customer base expands over time.
- Available cash increases substantially.
- Marketing expenditure increases alongside customer acquisition.

---

# 5. Return on Investment (ROI)

## Definition

Return on Investment measures the profitability of an investment
relative to its cost.

### Formula

ROI (%) = (Net Profit / Amount Invested) × 100

### Python Implementation

```python
ROI_data = (
    df['Net Profit'] / df['Amount Invested']
) * 100

plt.figure(figsize=(8, 5))

plt.bar(df.index, ROI_data)

plt.xlabel('Investment')
plt.ylabel('ROI (%)')
plt.title('ROI by Investment')

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

### Interpretation

ROI measures how efficiently invested capital generates profit.

A higher ROI indicates greater returns relative to the
investment amount.

### Important Consideration

Some observations have zero investment amounts.

Dividing by zero produces infinite ROI values, making
the original calculation unsuitable for those months.

A safer implementation is:

```python
import numpy as np

ROI_data = np.where(
    df['Amount Invested'] > 0,
    (df['Net Profit'] / df['Amount Invested']) * 100,
    np.nan
)
```

This excludes months without recorded investment
from the ROI calculation.

---

# 6. Cash Runway Analysis

## Definition

Cash runway measures how long a business can continue operating
using its available cash.

### Standard Formula

Cash Runway = Available Cash / Monthly Net Cash Burn

Where:

Monthly Net Cash Burn = Cash Outflows - Cash Inflows

This formula applies when cash outflows exceed cash inflows.

### Original Dataset Calculation

```python
runway = (
    df['Available Cash'] /
    (
        df['Monthly Cash Inflows'] -
        df['Monthly Cash Outflows']
    )
)
```

### Visualization

```python
plt.figure(figsize=(10, 5))

plt.plot(
    df['Month'],
    runway,
    marker='o',
    linestyle='--'
)

plt.xlabel('Month')
plt.ylabel('Runway (Months)')
plt.title('Cash Runway Over Time')

plt.xticks(rotation=45)
plt.grid(True)
plt.tight_layout()

plt.show()
```

### Interpretation

The plotted values generally increase from approximately
8 to 14 months.

However, the original calculation divides available cash
by positive net cash flow rather than net cash burn.

Therefore, these values represent a cash-to-net-inflow
ratio rather than standard cash runway.

### Recommended Calculation

```python
net_cash_burn = (
    df['Monthly Cash Outflows'] -
    df['Monthly Cash Inflows']
)

runway = np.where(
    net_cash_burn > 0,
    df['Available Cash'] / net_cash_burn,
    np.inf
)
```

When cash inflows exceed outflows, the business has no
positive net cash burn for that month.

---

# 7. Average Order Value (AOV)

## Definition

Average Order Value measures the average revenue generated
per customer order.

### Formula

AOV = Total Revenue / Number of Orders

### Python Implementation

```python
AOV = (
    df['Total Revenue'] /
    df['Number of Orders']
)

plt.figure(figsize=(8, 5))

plt.scatter(
    df['Number of Orders'],
    AOV
)

plt.xlabel('Number of Orders')
plt.ylabel('Average Order Value (AOV)')
plt.title('AOV vs Number of Orders')

plt.tight_layout()
plt.show()
```

### Interpretation

The scatter plot shows a positive relationship between
the number of orders and Average Order Value.

Key observations:

- Higher order volumes are associated with higher AOV.
- AOV increases as business activity expands.
- Revenue growth is supported by both order volume
  and order value.

### Business Significance

Increasing AOV can improve revenue without requiring
a proportional increase in customer acquisition.

Possible strategies include:

- Upselling
- Cross-selling
- Product bundling
- Premium product offerings

---

# 8. Customer Acquisition Cost (CAC)

## Definition

Customer Acquisition Cost measures the average marketing
and sales expenditure required to acquire one new customer.

### Formula

CAC = Marketing & Sales Costs / New Customers

### Python Implementation

```python
CAC = (
    df['Marketing & Sales Costs'] /
    df['New Customers']
)

plt.figure(figsize=(8, 5))

plt.boxplot(CAC.dropna())

plt.ylabel(
    'Customer Acquisition Cost (CAC)'
)

plt.title(
    'Distribution of Customer Acquisition Cost'
)

plt.tight_layout()
plt.show()
```

### Interpretation

The box plot indicates:

- Median CAC is approximately 1,200.
- Most CAC values fall between approximately
  1,130 and 1,310.
- One relatively high outlier appears near 1,760.

### Business Significance

Lower CAC generally indicates more efficient
customer acquisition.

Businesses should compare CAC with Customer
Lifetime Value (CLV) to evaluate long-term
acquisition profitability.

---

# 9. EBITDA Analysis

## Definition

EBITDA stands for:

Earnings Before Interest, Taxes, Depreciation,
and Amortization.

It is commonly used to assess operating profitability
before selected financing, tax, and non-cash expenses.

### Formula

EBITDA = Net Income
       + Interest Expense
       + Income Taxes
       + Depreciation
       + Amortization

### Python Implementation

```python
EBITDA = (
    df['Net Income']
    + df['Interest Expense']
    + df['Income Taxes']
    + df['Depreciation']
    + df['Amortization']
)
```

### EBITDA Components Visualization

```python
labels = [
    'Net Income',
    'Interest Expense',
    'Income Taxes',
    'Depreciation',
    'Amortization'
]

values = [
    df['Net Income'].sum(),
    df['Interest Expense'].sum(),
    df['Income Taxes'].sum(),
    df['Depreciation'].sum(),
    df['Amortization'].sum()
]

plt.figure(figsize=(10, 10))

plt.pie(
    values,
    labels=labels,
    autopct='%1.0f%%',
    startangle=40,
    textprops={'fontsize': 10},
    explode=[0, 0, 0.1, 0, 0]
)

legend_labels = [
    f'{label}: {value:,.0f}'
    for label, value in zip(labels, values)
]

plt.legend(
    legend_labels,
    title='Actual Values',
    loc='best',
    bbox_to_anchor=(1, 0.5)
)

plt.title('EBITDA Components')

plt.show()
```

### EBITDA Component Breakdown

| Component | Total Value | Approximate Share |
|-----------|-------------|-------------------|
| Net Income | 9,750,195 | 77% |
| Interest Expense | 260,451 | 2% |
| Income Taxes | 2,140,287 | 17% |
| Depreciation | 415,244 | 3% |
| Amortization | 152,998 | 1% |

### Total EBITDA

```python
total_ebitda = EBITDA.sum()

print(f"Total EBITDA: {total_ebitda:,.2f}")
```

Approximate total EBITDA:

**12.72 million**

### Interpretation

Net income represents the largest component
of the EBITDA reconciliation.

Income taxes account for approximately 17%
of the total.

Interest, depreciation, and amortization
represent comparatively smaller portions.

The results indicate substantial profitability
over the analyzed period.

---

# 10. Key Business Insights

## Revenue Growth

Revenue increases from approximately 451,117
to a maximum of 2.54 million.

This indicates substantial business expansion.

## Profitability

Average monthly net profit is approximately 406,258.

Maximum monthly net profit reaches approximately 807,522.

The business demonstrates strong profitability
throughout the dataset.

## Customer Growth

The ending customer count increases from 461
in the first month to a maximum of 2,161.

This indicates significant customer-base expansion.

## Order Performance

Average monthly order volume is approximately 1,088.

Higher order volumes are associated with
higher Average Order Values.

## Customer Acquisition

Median Customer Acquisition Cost is approximately 1,200.

Monitoring CAC alongside customer lifetime value
can help evaluate marketing efficiency.

## Cash Position

Available cash increases significantly over time.

This indicates improving liquidity, although cash runway
should be calculated using net cash burn rather than
positive net cash inflows.

---

# 11. Business Recommendations

### Improve Marketing Efficiency

Monitor CAC across acquisition channels.

Focus marketing expenditure on channels that
generate profitable long-term customers.

### Increase Average Order Value

Implement upselling and cross-selling strategies.

Introduce product bundles and premium offerings.

### Monitor Cash Flow

Track monthly cash inflows and outflows.

Maintain sufficient liquidity to support
operations and future investments.

### Optimize Operating Costs

Review operating expenses regularly.

Identify opportunities to improve operating margins
without reducing service quality.

### Monitor Profitability

Track net profit, EBITDA, and profit margins.

Compare monthly performance to identify
changes in business efficiency.

---

# 12. Technologies Used

- Python
- Pandas
- Matplotlib
- Microsoft Excel
- Jupyter Notebook

---

# 13. Conclusion

This project analyzes 24 months of business performance
using financial and customer-related metrics.

The analysis indicates:

- Strong revenue growth
- Increasing net profitability
- Expanding customer acquisition
- Increasing average order values
- Improving available cash balances
- Substantial EBITDA generation

These metrics provide useful insights into business
growth, operational efficiency, and financial performance.

Regular monitoring of these KPIs can support
data-driven business decisions.

---

**Project:** Business Metrics Analysis  
**Dataset:** business_metrics_dataset.xlsx  
**Language:** Python  
**Analysis Period:** January 2025 – December 2026
