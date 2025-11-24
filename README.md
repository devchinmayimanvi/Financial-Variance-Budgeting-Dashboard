# Financial-Variance-Budgeting-Dashboard

# Automated Financial Variance Analysis Dashboard 📊

## 📖 Project Overview
This project mimics a real-world financial reporting scenario where a finance team requires an automated dashboard to track **Budget vs. Actuals**. The goal was to eliminate manual Excel merging and provide a dynamic view of financial performance across different departments and GL accounts.

## ❓ The Business Problem
* **Data Silos:** Budget data exists in "Wide" Excel formats (months as columns), while Actuals come in daily incremental CSV dumps.
* **Granularity Mismatch:** Comparing Daily transactions (Actuals) against Monthly targets (Budget).
* **Reporting Lag:** Manual consolidation was taking hours/days at month-end.

## ⚙️ Technical Architecture
* **Tool:** Microsoft Power BI Desktop
* **ETL:** Power Query (M Language)
* **Data Model:** Star Schema

### 1. Data Transformation (ETL)
* **Folder Connector:** Configured Power Query to read from a `Monthly_Actuals` folder, allowing for auto-updates by simply dropping new CSV files into the directory.
* **Unpivoting:** Transformed the Budget Excel file from a cross-tab format to a normalized tabular format.
* **Date Normalization:** Transformed monthly text headers (e.g., "Jan-25") into "Start of Month" dates (01/01/2025) to enable relationship creation.

### 2. Data Modeling (Star Schema)
The model consists of two Fact Tables sharing Conformed Dimensions:
* **Fact_Budget** (Granularity: Monthly)
* **Fact_Actuals** (Granularity: Daily)
* **Dim_Calendar** (Source of Truth for Time Intelligence)
* **Dim_ChartOfAccounts** (Categorization for Revenue/Expenses)

### 3. Key DAX Features
**Dynamic Color Logic:**
Implemented a measure to handle polarity (Red isn't always bad).
```dax
Variance Color = 
VAR IsExpense = SELECTEDVALUE('Dim_ChartOfAccounts'[Type]) = "Expense"
RETURN
SWITCH( TRUE(),
    IsExpense && [Variance] > 0, "#D64550",  -- Expense Over Budget (Red)
    NOT(IsExpense) && [Variance] > 0, "#47B39C", -- Revenue Over Budget (Green)
    "#000000"
)
