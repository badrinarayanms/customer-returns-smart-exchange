# Customer Returns Analytics — Data Schema

## Overview

This document describes the schema used in the **Customer Returns Analytics & Smart Exchange** project.

The final Gold dataset contains **145 unique customer survey records** after duplicate removal and data-cleaning steps.

The schema is organized around four areas:

1. Customer profile
2. Purchase and return behavior
3. Exchange and replacement information
4. Gold-layer business-derived fields

---

## Data Architecture

```text
Raw Customer Survey Data
          ↓
       Bronze
          ↓
       Silver
          ↓
        Gold
          ↓
      Power BI
```

---

# 1. Customer Profile

| Column | Type | Description |
|---|---|---|
| `response_id` | Integer | Unique survey response identifier. |
| `age_group` | String | Customer age bucket: `18-25`, `26-35`, `36-45`, `46-55`, `56+`. |
| `gender` | String | Customer gender category. |
| `purchase_frequency` | String | Customer purchase frequency. |

### Example values

```text
age_group
18-25
26-35
36-45
46-55
56+

gender
Female
Male
Non-binary
Unknown

purchase_frequency
2-3 times a month
Once every 3 months
Rarely
```

---

# 2. Return Behavior

| Column | Type | Description |
|---|---|---|
| `ever_returned` | String | Whether the customer has returned a purchase: `Yes` / `No`. |
| `return_count` | Integer | Number of returns reported by the customer. |
| `return_count_missing` | Integer / Boolean Flag | Indicates whether `return_count` was originally missing. |
| `main_return_reason` | String | Main reason associated with the return. |
| `return_status` | String | Gold-derived business classification: `Returned` / `Not Returned`. |

### Main return reasons

```text
Wrong Size
Wrong Color
Poor Quality
Damaged Product
Product Didn't Match Description
Other
Changed Mind
Late Delivery
Unknown
Not Applicable
```

---

# 3. Exchange Information

| Column | Type | Description |
|---|---|---|
| `wanted_exchange` | String | Whether the customer wanted an exchange. |
| `exchange_available` | String | Whether an exchange was available. |
| `replacement_unavailable` | String | Reason the requested replacement was unavailable. |
| `what_happened_when_unavailable` | String | Outcome when the requested replacement was unavailable. |

### Replacement-unavailable categories

```text
No Stock in Desired Size
No Stock in Desired Color
Other
Exchange Not Offered
Unknown
Not Applicable
```

---

# 4. Gold Business-Derived Fields

The Gold layer adds fields that transform cleaned survey data into business-ready analytical attributes.

## `return_status`

Derived from:

```text
ever_returned
```

Logic:

```text
ever_returned = Yes
        ↓
Returned

ever_returned = No
        ↓
Not Returned
```

Purpose:

Provides a business-friendly field for Power BI analysis.

---

## `smart_exchange_candidate`

Identifies customers who may be suitable for a Smart Exchange workflow.

Current business rule:

```text
wanted_exchange = Yes
        AND
replacement_unavailable IN
    (
        "No Stock in Desired Size",
        "No Stock in Desired Color"
    )

        ↓

smart_exchange_candidate = Yes
```

All other records are classified as:

```text
smart_exchange_candidate = No
```

---

## `smart_exchange_type`

Classifies Smart Exchange candidates according to the availability problem.

Current classifications include:

```text
Size Availability
Color Availability
Other Exchange Issue
Not a Candidate
```

For the final candidate-focused analysis:

```text
Size Availability     40
Color Availability    11
```

Total:

```text
51 Smart Exchange Candidates
```

---

# 5. Ingestion Metadata

| Column | Type | Description |
|---|---|---|
| `ingested_at` | Timestamp | Timestamp associated with data ingestion. |

This field is used as ingestion metadata and **should not be treated as the actual customer return date**.

---

# 6. Data Cleaning Rules

The Silver layer performs the main data-quality preparation before Gold transformation.

### Duplicate handling

```text
150 raw records
      ↓
5 duplicates removed
      ↓
145 unique records
```

### Missing values

Missing categorical values are standardized according to the business context.

Examples include:

```text
Unknown
Not Applicable
```

### Category standardization

Inconsistent capitalization, spacing, and spelling are standardized.

Examples of values that required normalization included variations such as:

```text
Wrong Szie
wrong size
Wrong colour
female
```

These are standardized into consistent business categories.

---

# 7. Key Gold Metrics

The Gold table supports the following Power BI metrics:

| Metric | Result |
|---|---:|
| Total Customers | 145 |
| Returned Customers | 102 |
| Return Rate | 70.34% |
| Wanted Exchanges | 72 |
| Smart Exchange Candidates | 51 |
| Smart Exchange Rate | 70.83% |

---

# 8. Important Analytical Relationships

The schema allows analysis across:

```text
Customer Profile
       ↓
Return Behavior
       ↓
Exchange Preference
       ↓
Replacement Availability
       ↓
Smart Exchange Opportunity
```

The most important root-cause relationship identified is:

```text
Wrong Size Return
       ↓
Wanted Exchange
       ↓
No Stock in Desired Size
       ↓
23 customers
```

---

# 9. Data Limitations

The current dataset does **not** provide reliable fields for:

- Revenue
- Order value
- Refund amount
- Product category
- Geographic region
- Reliable historical return date
- Revenue saved through exchanges

Therefore, these metrics should not be derived or presented as measured facts from this dataset:

```text
Monthly return trend
Regional return rate
Product-category return rate
Revenue impact
Refund savings
Revenue saved through Smart Exchange
```

---

# 10. Power BI Usage

The Gold schema is designed to support the following dashboard analyses:

### KPI Analysis

```text
Total Customers
Returned Customers
Return Rate
Smart Exchange Candidates
Smart Exchange Rate
```

### Return Analysis

```text
Return Rate by Age Group
Top Return Reasons
```

### Exchange Analysis

```text
Exchange Problems
Replacement Availability
```

### Root Cause Analysis

```text
main_return_reason
        ×
replacement_unavailable
```

### Smart Exchange Analysis

```text
smart_exchange_candidate
        +
smart_exchange_type
```

---

## Schema Summary

```text
Customer Profile
    ├── response_id
    ├── age_group
    ├── gender
    └── purchase_frequency

Return Behavior
    ├── ever_returned
    ├── return_count
    ├── return_count_missing
    ├── main_return_reason
    └── return_status

Exchange Information
    ├── wanted_exchange
    ├── exchange_available
    ├── replacement_unavailable
    └── what_happened_when_unavailable

Gold Business Logic
    ├── smart_exchange_candidate
    └── smart_exchange_type

Metadata
    └── ingested_at
```
