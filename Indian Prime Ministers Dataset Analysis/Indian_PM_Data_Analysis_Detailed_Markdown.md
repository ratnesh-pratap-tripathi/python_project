# Indian Prime Ministers Data Analysis — Detailed Markdown Notes

## 1. Project Overview

This notebook performs a **Pandas-based data analysis** of an Indian Prime Ministers dataset.

The original dataset is loaded from:

```python
df = pd.read_excel('inaianPMlist.xlsx')
```

The notebook works mainly with these columns:

- `Prime Minister`
- `Start Period`
- `End Period`

Initially, the dataset also contains `S.No`, but the notebook later uses `S.No` as the DataFrame index.

The analysis focuses on:

- Understanding the dataset structure
- Cleaning date columns
- Converting text dates into datetime values
- Detecting missing values
- Finding unique Prime Ministers
- Counting Prime Minister records
- Finding earliest/latest dates
- Calculating tenure in days and years
- Finding longest and shortest tenures
- Sorting records
- Filtering Prime Ministers by tenure
- Calculating average tenure
- Creating a final analytical summary

---

# 2. Import Pandas

The notebook starts by importing Pandas:

```python
import pandas as pd
```

### Why Pandas?

Pandas is used for:

- Reading Excel files
- Creating and manipulating DataFrames
- Cleaning data
- Working with dates
- Filtering rows
- Sorting data
- Performing calculations
- Generating analytical summaries

---

# 3. Load the Excel Dataset

```python
df = pd.read_excel('inaianPMlist.xlsx')
```

This reads the Excel file into a Pandas DataFrame called `df`.

The notebook initially displays the first five records using:

```python
df.head()
```

The visible columns at this stage are:

```text
S.No
Prime Minister
Start Period
End Period
```

Example records include:

| S.No | Prime Minister | Start Period | End Period |
|---:|---|---|---|
| 1 | Pandit Jawaharlal Nehru | 15th Aug 1947 | 27th May 1964 |
| 2 | Gulzarilal Nanda (interim) | 27th May 1964 | 9th Jun 1964 |
| 3 | Lal Bahadur Shastri | 9th June 1964 | 11th January 1966 |
| 4 | Gulzarilal Nanda | 11th January 1966 | 24th January 1966 |
| 5 | Indira Gandhi | 24th January 1966 | 24th March 1977 |

---

# 4. Date Data Cleaning

The original date values are stored as text and contain different month formats.

For example:

```text
15th Aug 1947
9th Jun 1964
9th June 1964
11th January 1966
```

This is important because `Aug` is a short month name while `June` and `January` are full month names.

The notebook uses:

```python
for col in ['Start Period', 'End Period']:
    df[col] = df[col].str.strip()
    df[col] = pd.to_datetime(df[col], format='mixed')
```

### Step 1 — Remove extra spaces

```python
df[col] = df[col].str.strip()
```

`.str.strip()` removes unnecessary spaces from the beginning and end of text.

### Step 2 — Convert to datetime

```python
pd.to_datetime(df[col], format='mixed')
```

`format='mixed'` allows Pandas to infer different date formats for individual values.

After conversion:

```text
Start Period    datetime64[ns]
End Period      datetime64[ns]
```

This conversion is essential because date calculations such as subtraction, minimum, maximum, and sorting work correctly with datetime values.

---

# 5. Inspect Dataset Structure

The notebook uses:

```python
df.info()
```

The dataset contains:

- **18 rows**
- **4 columns** before `S.No` is made the index
- `S.No` → `int64`
- `Prime Minister` → `object`
- `Start Period` → `datetime64[ns]`
- `End Period` → `datetime64[ns]`

The `End Period` column has **17 non-null values**, meaning one value is missing.

The missing End Period belongs to the last record in the notebook:

```text
Narendra Modi
Start Period: 2014-05-26
End Period: NaT
```

---

# 6. Set S.No as the Index

The notebook uses:

```python
df = df.set_index('S.No')
```

This changes the DataFrame index from the default `0, 1, 2...` to the `S.No` values.

After this operation, the main columns become:

```text
Prime Minister
Start Period
End Period
```

For example:

```text
S.No  Prime Minister             Start Period  End Period
1     Pandit Jawaharlal Nehru    1947-08-15    1964-05-27
2     Gulzarilal Nanda (interim) 1964-05-27    1964-06-09
```

---

# 7. Display Column Names

The notebook checks the column names using:

```python
df.columns.to_list()
```

The result is:

```python
['Prime Minister', 'Start Period', 'End Period']
```

This is useful because it confirms the exact column names that should be used in subsequent Pandas operations.

---

# 8. Question 1 — How Many Prime Ministers Are Present?

### Code

```python
count = df['Prime Minister'].nunique()
count
```

### Result

```text
16
```

### Explanation

`nunique()` counts unique values.

The dataset has **18 records**, but only **16 unique Prime Minister names** because some Prime Ministers appear more than once due to separate periods of service.

---

# 9. Question 2 — How Many Total Records Are Present?

### Code

```python
total_records = df.shape[0]
total_records
```

### Result

```text
18
```

### Explanation

`df.shape` returns:

```text
(number_of_rows, number_of_columns)
```

Therefore:

```python
df.shape[0]
```

returns the number of rows.

The dataset contains **18 records**.

---

# 10. Question 3 — How Many Columns Are Present?

### Code

```python
total_columns = df.shape[1]
total_columns
```

### Result

```text
3
```

### Explanation

After `S.No` is converted into the index, the DataFrame has three data columns:

```text
Prime Minister
Start Period
End Period
```

---

# 11. Question 4 — Find All Unique Prime Ministers

### Code

```python
prime_ministers = df['Prime Minister'].unique()
prime_ministers
```

### Unique names found in the notebook

```text
Pandit Jawaharlal Nehru
Gulzarilal Nanda (interim)
Lal Bahadur Shastri
Gulzarilal Nanda
Indira Gandhi
Morarji Desai
Charan Singh
Rajiv Gandhi
Vishwa Pratap Singh
Chandra Shekhar
P. V Narasimha Rao
Atal Bihari Vajpayee
H. D Deve Gowda
Inder Kumar Gujral
Dr Manmohan Singh
Narendra Modi
```

### Explanation

`unique()` returns each distinct value from a Series.

---

# 12. Question 5 — Count How Many Times Each Prime Minister Appears

### Code

```python
pm_counts = df['Prime Minister'].value_counts()
pm_counts
```

### Important result

The notebook shows:

```text
Indira Gandhi          2
Atal Bihari Vajpayee   2
```

The other Prime Minister names appear once in the dataset.

### Explanation

`value_counts()` counts the frequency of each unique value.

This is particularly useful when one person has multiple separate records.

---

# 13. Question 6 — Check Missing Values

### Code

```python
missing_values = df.isnull().sum()
missing_values
```

### Result

```text
Prime Minister    0
Start Period      0
End Period        1
```

### Explanation

There are no missing values in:

- `Prime Minister`
- `Start Period`

There is **one missing value in `End Period`**.

The missing End Period appears for Narendra Modi in the notebook.

After datetime conversion, the missing value is represented as:

```text
NaT
```

`NaT` means **Not a Time** and is used by Pandas for missing datetime values.

---

# 14. Question 7 — Find Records With Missing End Period

### Code

```python
result = df[df['End Period'].isna()]
result
```

### Result

The notebook returns:

```text
Prime Minister    Narendra Modi
Start Period      2014-05-26
End Period        NaT
```

### Explanation

`isna()` identifies missing values.

The condition:

```python
df['End Period'].isna()
```

creates a Boolean filter that selects only rows where `End Period` is missing.

---

# 15. Question 8 — Find Prime Minister With Earliest Start Period

### Code

```python
idx = df['Start Period'].idxmin()
df.loc[idx]
```

### Result

The notebook identifies:

```text
Prime Minister    Pandit Jawaharlal Nehru
Start Period      1947-08-15
End Period        1964-05-27
```

### Explanation

`idxmin()` returns the index label of the minimum value.

Because `Start Period` is a datetime column, the earliest date can be found directly.

---

# 16. Question 9 — Find Prime Minister With Latest Start Period

### Code

```python
idx = df['Start Period'].idxmax()
df.loc[idx]
```

### Result

The notebook identifies:

```text
Prime Minister    Narendra Modi
Start Period      2014-05-26
End Period        NaT
```

### Explanation

`idxmax()` returns the index label corresponding to the maximum date.

The latest Start Period in this dataset belongs to Narendra Modi.

---

# 17. Question 10 — Find Earliest Start Period

### Code

```python
earliest = df['Start Period'].min()
earliest
```

### Result

```text
1947-08-15
```

### Explanation

`min()` returns the earliest datetime value.

---

# 18. Question 11 — Find Latest End Period

### Code

```python
latest = df['End Period'].max()
latest
```

### Result

```text
2014-05-17
```

### Explanation

`max()` returns the latest available End Period.

The missing `NaT` value does not become the maximum; Pandas ignores missing datetime values in this calculation.

---

# 19. Question 12 — Calculate Tenure in Days

### Code

```python
df['Tenure Days'] = (
    df['End Period'] - df['Start Period']
).dt.days
```

### Explanation

First:

```python
df['End Period'] - df['Start Period']
```

calculates the time difference.

Then:

```python
.dt.days
```

extracts the difference in days.

The resulting column is:

```text
Tenure Days
```

Examples from the notebook:

| Prime Minister | Tenure Days |
|---|---:|
| Pandit Jawaharlal Nehru | 6130 |
| Gulzarilal Nanda (interim) | 13 |
| Lal Bahadur Shastri | 581 |
| Gulzarilal Nanda | 13 |
| Indira Gandhi | 4077 |
| Morarji Desai | 856 |
| Rajiv Gandhi | 1858 |
| Dr Manmohan Singh | 3647 |
| Narendra Modi | NaN |

Narendra Modi has `NaN` because the `End Period` is missing.

---

# 20. Question 13 — Calculate Tenure in Years

### Code

```python
df['Tenure Years'] = (
    (df['End Period'] - df['Start Period']).dt.days / 365.25
)
```

### Why divide by 365.25?

A year is approximately 365.25 days when accounting for leap years over time.

The notebook uses this calculation to convert tenure from days into approximate years.

### Example results

| Prime Minister | Tenure Years |
|---|---:|
| Pandit Jawaharlal Nehru | 16.78 |
| Indira Gandhi | 11.16 |
| Rajiv Gandhi | 5.09 |
| Atal Bihari Vajpayee | 6.18 |
| Dr Manmohan Singh | 9.98 |

The notebook rounds displayed values in one step using:

```python
df[['Prime Minister', 'Tenure Years']].round(2)
```

---

# 21. Question 14 — Find the Longest Tenure

### Code

```python
df['Tenure Days'] = (
    df['End Period'] - df['Start Period']
).dt.days

idx = df['Tenure Days'].idxmax()

df.loc[idx]
```

### Result

The longest tenure in the notebook belongs to:

```text
Pandit Jawaharlal Nehru
```

with:

```text
Tenure Days     6130
Tenure Years    16.783025
```

### Explanation

`idxmax()` finds the row containing the maximum tenure value.

---

# 22. Question 15 — Find the Shortest Tenure

### Code

```python
df['Tenure Days'] = (
    df['End Period'] - df['Start Period']
).dt.days

idx = df['Tenure Days'].idxmin()

df.loc[idx]
```

### Result

The notebook identifies:

```text
Gulzarilal Nanda (interim)
```

with:

```text
Tenure Days    13
```

### Explanation

`idxmin()` finds the row containing the smallest non-missing tenure value.

---

# 23. Question 16 — Sort Prime Ministers by Start Period

### Code

```python
result = df.sort_values(
    by='Start Period',
    ascending=True
)
```

### Explanation

`sort_values()` sorts rows according to a column.

```python
ascending=True
```

means earliest date comes first.

The sorted dataset begins with:

```text
Pandit Jawaharlal Nehru
Gulzarilal Nanda (interim)
Lal Bahadur Shastri
Gulzarilal Nanda
Indira Gandhi
...
```

and the latest start date is Narendra Modi.

---

# 24. Question 17 — Sort by End Period From Newest to Oldest

### Code

```python
result = df.sort_values(
    by='End Period',
    ascending=False
)
```

### Explanation

Here:

```python
ascending=False
```

means the latest available End Period appears first.

The notebook's sorted result begins with:

```text
Dr Manmohan Singh
Atal Bihari Vajpayee
Inder Kumar Gujral
H. D Deve Gowda
...
```

The missing `NaT` value for Narendra Modi appears at the end of the displayed result.

---

# 25. Question 18 — Find Prime Ministers Who Served More Than 5 Years

### Code

```python
df['Tenure Years'] = (
    (df['End Period'] - df['Start Period']).dt.days / 365.25
)

result = df[df['Tenure Years'] > 5]
```

### Result

The notebook identifies these records:

| Prime Minister | Tenure Years |
|---|---:|
| Pandit Jawaharlal Nehru | 16.78 |
| Indira Gandhi | 11.16 |
| Rajiv Gandhi | 5.09 |
| Atal Bihari Vajpayee | 6.18 |
| Dr Manmohan Singh | 9.98 |

### Explanation

The condition:

```python
df['Tenure Years'] > 5
```

returns only rows where tenure is greater than five years.

---

# 26. Question 19 — Calculate Average Tenure

### Code

```python
df['Tenure Years'] = (
    (df['End Period'] - df['Start Period']).dt.days / 365.25
)

average_tenure = df['Tenure Years'].mean()
average_tenure
```

### Result

The notebook gives approximately:

```text
3.9267 years
```

or approximately:

```text
3.93 years
```

### Explanation

`mean()` calculates the average of the available tenure values.

Because Narendra Modi has a missing End Period and therefore a missing tenure, that row does not contribute a numeric value to the mean.

---

# 27. Question 20 — Create a Final Analytical Summary

### Code

```python
df['Tenure Years'] = (
    (df['End Period'] - df['Start Period']).dt.days / 365.25
)

summary = df[
    ['Prime Minister', 'Start Period', 'End Period', 'Tenure Years']
]

summary
```

### Purpose

This creates a clean final analytical table containing:

- Prime Minister name
- Start Period
- End Period
- Calculated Tenure Years

This is useful for reporting, visualization, and further analysis.

---

# 28. Complete Data Analysis Workflow

The notebook follows this overall workflow:

```text
Excel File
    ↓
Read Data with Pandas
    ↓
Inspect Data
    ↓
Clean Date Strings
    ↓
Convert Dates to Datetime
    ↓
Check Data Types
    ↓
Set S.No as Index
    ↓
Explore Columns and Unique Values
    ↓
Check Missing Values
    ↓
Analyze Start/End Dates
    ↓
Calculate Tenure
    ↓
Find Longest/Shortest Tenure
    ↓
Filter and Sort Data
    ↓
Calculate Average Tenure
    ↓
Create Final Summary
```

---

# 29. Important Pandas Functions Used

## `pd.read_excel()`

Reads Excel data.

```python
df = pd.read_excel('inaianPMlist.xlsx')
```

## `head()`

Shows the first rows.

```python
df.head()
```

## `tail()`

Shows the last rows.

```python
df.tail()
```

## `columns.to_list()`

Returns column names as a Python list.

```python
df.columns.to_list()
```

## `str.strip()`

Removes leading and trailing spaces.

```python
df['Start Period'].str.strip()
```

## `pd.to_datetime()`

Converts values to datetime.

```python
pd.to_datetime(df['Start Period'], format='mixed')
```

## `info()`

Shows DataFrame structure and data types.

```python
df.info()
```

## `set_index()`

Sets a column as the DataFrame index.

```python
df.set_index('S.No')
```

## `nunique()`

Counts unique values.

```python
df['Prime Minister'].nunique()
```

## `unique()`

Returns unique values.

```python
df['Prime Minister'].unique()
```

## `value_counts()`

Counts the frequency of values.

```python
df['Prime Minister'].value_counts()
```

## `isnull().sum()`

Counts missing values.

```python
df.isnull().sum()
```

## `isna()`

Identifies missing values.

```python
df['End Period'].isna()
```

## `idxmin()`

Returns the index of the minimum value.

```python
df['Start Period'].idxmin()
```

## `idxmax()`

Returns the index of the maximum value.

```python
df['Start Period'].idxmax()
```

## `min()`

Finds the minimum value.

```python
df['Start Period'].min()
```

## `max()`

Finds the maximum value.

```python
df['End Period'].max()
```

## `sort_values()`

Sorts DataFrame rows.

```python
df.sort_values(by='Start Period')
```

## `mean()`

Calculates the average.

```python
df['Tenure Years'].mean()
```

---

# 30. Key Findings From the Notebook

Based strictly on the executed notebook:

### Dataset size

- 18 records
- 3 data columns after using `S.No` as the index

### Unique Prime Ministers

- 16 unique names

### Missing data

- `Prime Minister`: 0 missing
- `Start Period`: 0 missing
- `End Period`: 1 missing

### Missing End Period

The missing End Period is associated with:

```text
Narendra Modi
```

### Earliest Start Period

```text
Pandit Jawaharlal Nehru
15 August 1947
```

### Latest Start Period

```text
Narendra Modi
26 May 2014
```

### Latest available End Period

```text
17 May 2014
```

### Longest tenure in the notebook

```text
Pandit Jawaharlal Nehru
6130 days
≈ 16.78 years
```

### Shortest completed tenure in the notebook

```text
Gulzarilal Nanda (interim)
13 days
```

### Average completed tenure

```text
≈ 3.93 years
```

### Prime Ministers/records with tenure greater than 5 years

- Pandit Jawaharlal Nehru
- Indira Gandhi
- Rajiv Gandhi
- Atal Bihari Vajpayee
- Dr Manmohan Singh

---

# 31. Interview Explanation of This Project

A simple interview explanation can be:

> "I worked on an Indian Prime Ministers dataset using Python and Pandas. I first loaded the Excel file and inspected its structure. The Start Period and End Period columns contained dates stored as text with mixed date formats, so I cleaned the values and converted them into datetime format using `pd.to_datetime()` with `format='mixed'`. I then checked missing values, identified unique Prime Ministers, calculated tenure in days and years, found the longest and shortest tenures, filtered records based on tenure, sorted the data by dates, calculated average tenure, and created a final summary table."

---

# 32. Main Learning From This Notebook

This project demonstrates a practical Pandas workflow:

**Load → Inspect → Clean → Transform → Analyze → Filter → Sort → Summarize**

The most important skills demonstrated are:

1. Reading Excel files
2. DataFrame inspection
3. Date cleaning
4. Date conversion
5. Missing-value detection
6. Unique-value analysis
7. Frequency analysis
8. Date-based filtering
9. Date arithmetic
10. Sorting
11. Aggregation
12. Creating derived columns
13. Building an analytical summary

