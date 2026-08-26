# Customer Returns Analytics & Smart Exchange

**End-to-end Data Analytics Project | Databricks • PySpark • SQL • Power BI**

> **Business Question:** Why are return rates high, and how can we reduce them?

---

## 📌 Project Overview

This project analyzes customer-return survey data to understand the main drivers behind product returns and identify opportunities to convert eligible return journeys into exchanges.

The project follows an end-to-end analytics workflow:

```text
Raw Customer Survey Data
          ↓
      Databricks
          ↓
       Bronze
          ↓
       Silver
          ↓
        Gold
          ↓
      Power BI
          ↓
   Root Cause Analysis
          ↓
    Smart Exchange
```

The final solution focuses on **return reasons, replacement availability, and Smart Exchange opportunities**.

---

## 🎯 Business Problem

A high return rate can create additional operational costs, inventory pressure, and poor customer experience.

The key questions addressed in this project are:

1. How many customers are returning products?
2. What are the main reasons for returns?
3. When customers want an exchange, why is the replacement unavailable?
4. How significant are size and color availability problems?
5. Which customers could potentially be retained through a Smart Exchange workflow?
6. What business action can reduce avoidable returns?

---

## 📊 Dataset

The final Gold dataset contains:

- **145 unique customer survey records**
- Customer demographic information
- Purchase frequency
- Return behavior
- Return reasons
- Exchange preferences
- Replacement availability
- Gold-layer business classifications

### Main schema

| Column | Description |
|---|---|
| `response_id` | Unique survey response identifier |
| `age_group` | Customer age bucket |
| `gender` | Customer gender |
| `purchase_frequency` | Customer purchase frequency |
| `ever_returned` | Whether the customer has returned a purchase |
| `return_count` | Number of reported returns |
| `return_count_missing` | Missing-value indicator for return count |
| `main_return_reason` | Main reason for return |
| `wanted_exchange` | Whether the customer wanted an exchange |
| `exchange_available` | Whether an exchange was available |
| `replacement_unavailable` | Reason the replacement was unavailable |
| `what_happened_when_unavailable` | Outcome when replacement was unavailable |
| `ingested_at` | Data ingestion timestamp |
| `return_status` | Gold-derived Returned / Not Returned classification |
| `smart_exchange_candidate` | Gold-derived Smart Exchange eligibility |
| `smart_exchange_type` | Gold-derived Smart Exchange classification |

---

# 🏗️ Data Engineering Pipeline

## Bronze Layer

The raw customer survey data is ingested into Databricks.

```text
Raw CSV
  ↓
Bronze Table
```

Purpose:

- Preserve the ingested source data
- Establish the initial structured dataset
- Add ingestion metadata

---

## Silver Layer

The Silver layer performs data cleaning and standardization.

Main activities:

- Duplicate removal
- Null/missing-value handling
- Categorical value standardization
- Inconsistent spelling correction
- Return-count missing-value handling
- Conditional logic for exchange-related fields
- Data quality validation

Example:

```text
150 raw records
      ↓
5 duplicates removed
      ↓
145 unique records
```

The cleaned Silver table becomes the reliable foundation for business analysis.

---

## Gold Layer

The Gold layer converts cleaned data into business-ready analytical fields.

### `return_status`

```text
ever_returned = Yes
        ↓
Returned

ever_returned = No
        ↓
Not Returned
```

### `smart_exchange_candidate`

A customer is classified as a Smart Exchange candidate when:

```text
wanted_exchange = Yes
AND
replacement unavailable because of:
    - No Stock in Desired Size
    OR
    - No Stock in Desired Color
```

### `smart_exchange_type`

Candidates are further classified into categories such as:

- Size Availability
- Color Availability

The Gold layer is then consumed by Power BI.

---

# 📈 Key Findings

## Overall Returns

| KPI | Result |
|---|---:|
| Total Customers | **145** |
| Returned Customers | **102** |
| Return Rate | **70.34%** |
| Wanted Exchanges | **72** |
| Smart Exchange Candidates | **51** |
| Smart Exchange Rate | **70.83%** |

---

## 🔴 Main Return Reasons

The largest return reasons are:

| Return Reason | Customers |
|---|---:|
| Wrong Size | **47** |
| Wrong Color | **24** |
| Poor Quality | 7 |
| Damaged Product | 6 |
| Product Didn't Match Description | 5 |
| Other | 5 |
| Changed Mind | 4 |
| Late Delivery | 3 |
| Unknown | 1 |

### Key observation

**Wrong Size and Wrong Color are the dominant return reasons.**

This makes replacement size/color availability an important area for investigation.

---

## 🔄 Replacement Availability Problem

The replacement-unavailable analysis shows:

| Replacement Issue | Customers |
|---|---:|
| No Stock in Desired Size | **40** |
| No Stock in Desired Color | **11** |
| Other | 9 |
| Not Applicable | 7 |
| Exchange Not Offered | 4 |
| Unknown | 1 |

The strongest operational issue is **replacement stock availability**, particularly desired size availability.

---

# 🔎 Root Cause Analysis

The Power BI root-cause analysis connects:

```text
Main Return Reason
        +
Replacement Unavailable
```

One of the strongest relationships identified is:

```text
Wrong Size Return
        ↓
Wanted Exchange
        ↓
No Stock in Desired Size
        ↓
23 customers
```

This shows that some return journeys may be driven not simply by dissatisfaction with the product, but by the inability to obtain the desired replacement.

---

# 💡 Final Solution — Smart Exchange

## Problem

A customer wants to exchange a product, but the requested size or color is unavailable.

The traditional journey may become:

```text
Customer
   ↓
Return
   ↓
Refund / Return Processing
```

## Proposed Smart Exchange

```text
Customer wants exchange
          ↓
Check desired replacement
          ↓
Unavailable?
          ↓
Find suitable alternative
          ↓
Offer Smart Exchange
          ↓
Customer completes exchange
```

The goal is to **convert eligible return journeys into exchanges** instead of automatically processing a return.

---

## Smart Exchange Opportunity

The Gold analysis identified:

### **51 Smart Exchange Candidates**

Broken down into:

```text
Size Availability     40
Color Availability    11
```

This provides a concrete business opportunity for an exchange-first workflow.

---

# 📊 Power BI Dashboard

The Power BI report contains **4 pages**.

### Page 1 — Executive Dashboard

Provides:

- Total Customers
- Returned Customers
- Return Rate
- Smart Exchange Candidates
- Smart Exchange Rate
- Return Rate by Age Group
- Top Return Reasons
- Exchange Problems
- Root Cause Analysis
- Smart Exchange Opportunity
- Business Recommendation

### Page 2 — Root Cause Analysis

Focuses on:

```text
Return Reason
      ×
Replacement Availability
```

### Page 3 — Smart Exchange

Shows the Smart Exchange concept and opportunity.

### Page 4 — Solution / Process

Shows the current problem and proposed Smart Exchange workflow.

---

## 🖼️ Dashboard Screenshots

### Executive Dashboard

[customer_returns.pdf](https://github.com/user-attachments/files/31485601/customer_returns.pdf)


---

# 🛠️ Technology Stack

- **Python / PySpark**
- **Databricks**
- **SQL**
- **Power BI**
- **DAX**
- **GitHub**

---

# 📁 Repository Structure

```text
customer-returns-smart-exchange/
│
├── README.md
│
├── notebooks/
│   └── customer_returns.ipynb
│   
│
├── powerbi/
│   ├── customer_returns.pbix
│   └── customer_returns.pdf
│
├── docs/
│   └── customer_returns_project_summary.pdf
│
└── data/
    ├── raw_data.csv
    ├── cleaned_data.csv
    └── schema.md
```

---

# 📄 Project Documentation

The detailed project documentation covers:

- Business problem
- Data survey schema
- Required analytical questions
- Key findings
- Final Smart Exchange solution
- Business recommendation
- Data limitations

See:

`docs/customer_returns_project_summary.pdf`

---

# ⚠️ Data Limitations

The dataset does **not** contain reliable fields for:

- Revenue
- Order value
- Refund amount
- Product category
- Geographic region
- Reliable historical return date
- Revenue saved through exchanges

Therefore, the project intentionally does **not** claim:

- Revenue impact
- Refund savings
- Regional return rates
- Product-category return rates
- Monthly return trends
- Year-over-year return trends

This keeps the analysis grounded in the available data.

---

# 🎯 Business Recommendation

The analysis suggests focusing on two areas:

### 1. Improve replacement availability

Prioritize inventory availability for popular:

- Sizes
- Colors

### 2. Introduce Smart Exchange

When a customer wants an exchange but their desired replacement is unavailable:

```text
Check availability
      ↓
Find suitable alternative
      ↓
Offer Smart Exchange
      ↓
Complete exchange
```

This creates an opportunity to reduce avoidable returns while improving the customer experience.

---

# 🚀 Project Outcome

This project demonstrates an end-to-end analytics workflow:

```text
Data Ingestion
      ↓
Data Cleaning
      ↓
Data Quality Validation
      ↓
Business Transformation
      ↓
KPI Development
      ↓
Root Cause Analysis
      ↓
Power BI Visualization
      ↓
Business Recommendation
```

The final analysis moves beyond simply reporting return numbers and proposes a **data-driven Smart Exchange solution** based on the observed return and replacement-availability patterns.

---

