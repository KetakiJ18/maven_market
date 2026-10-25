# Maven Market Power BI Dashboard

A comprehensive, end-to-end business intelligence solution built in Power BI Desktop for **Maven Market**, a multi-national retail corporation. This dashboard transforms raw data across transactions, returns, stores, customers, and products into actionable insights regarding store performance, product profitability, and regional growth.

---

## 🛠️ Architecture & Data Modeling

### 1. Data Connection & ETL (Power Query)
* **Sources:** Multi-table CSV datasets covering `Customers`, `Products`, `Stores`, `Regions`, `Transactions (1997-1998)`, and `Returns`.
* **ETL Transformations:** 
  * Merged historical transaction tables (1997 and 1998).
  * Standardized date/time fields and verified data types across all columns.
  * Formatted geographical fields (City, State, Country) for map visualizations.

### 2. The Data Model (Star/Snowflake Schema)
* Built a robust relational model utilizing a **Star Schema** to optimize query performance.
* Disabled auto-detect relationships to manually enforce clean **1-to-many directional filters** between lookup/dimension tables and fact tables (`Transactions` and `Returns`).

---

## 📐 Advanced DAX Formulas & Calculated Columns

### Calculated Columns
* **Price Tier:** Segmented products into pricing buckets.
  ```dax
  Price Tier = IF(Products[product_retail_price] > 3, "High", IF(Products[product_retail_price] > 1, "Mid", "Low"))
  ```
* **Years Since Remodel:** Tracks store aging metrics dynamically.
  ```dax
  Years Since Remodel = DATEDIFF(Stores[last_remodel_date], TODAY(), YEAR)
  ```

### Key DAX Measures
* **Total Revenue:** Iterates through transaction line-items to calculate total gross sales.
  ```dax
  Total Revenue = SUMX(Transactions, Transactions[quantity] * RELATED(Products[product_retail_price]))
  ```
* **Total Profit:** Evaluates take-home margins by factoring in product costs.
  ```dax
  Total Profit = [Total Revenue] - SUMX(Transactions, Transactions[quantity] * RELATED(Products[product_cost]))
  ```
* **Return Rate:** Tracks product quality and customer satisfaction issues.
  ```dax
  Return Rate = DIVIDE(SUM(Returns[return_quantity]), SUM(Transactions[quantity]), 0)
  ```
* **Last Month Transactions:** Compares historical performance using time-intelligence functions.
  ```dax
  Last Month Transactions = CALCULATE([Total Transactions], DATEADD('Calendar'[date], -1, MONTH))
  ```

---

## 📈 Key Insights & Features

* **Executive Topline Metrics:** High-level KPIs tracking total revenue, gross profit, overall return rates, and distinct customer foot traffic.
* **Product Performance Matrix:** Conditional formatting-driven top 30 product performance views, exposing high-return products vs. top revenue drivers.
* **Geographical Matrix:** Visual maps and breakdowns analyzing store performance across regional districts, states, and cities.
* **Dynamic Slicing:** Multi-layered filters allowing stakeholders to drill down by custom time frames, income levels, occupations, and product brands.
