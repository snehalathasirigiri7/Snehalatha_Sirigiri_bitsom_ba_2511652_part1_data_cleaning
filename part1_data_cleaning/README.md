# Supply Chain & Retail Sales Data Cleaning and Analytics Pipeline

## 1. Problem Summary
In this project, a raw retail transaction dataset (raw_orders.xlsx) containing mixed text formats, missing operational values, duplicated tracking records, and structural business rule violations was audited and optimized. Without a systematic data cleaning pipeline, anomalous records—such as negative discounts or products shipping before they were ordered—would corrupt corporate financial metrics, performance audits, and downstream machine learning models. 

This project establishes an end-to-end audit and normalization process inside Excel and documents structural findings via reproducible metrics without silently overwriting historical database records.

---

## 2. Dataset Description
The source dataset consists of 21 core data dimensions across transactional logistics, client segments, geographical regions, and inventory financials:

* **Transactional Dimensions:** order_id, order_date, ship_date, order_status, payment_status
* **Customer Demographics:** customer_id, customer_name, segment
* **Geographical Vectors:** region, state, city
* **Product Taxonomy:** category, sub_category, product_name
* **Financial & Inventory Metrics:** quantity, unit_price, discount, sales, cost, profit

---

## 3. Tools & Engineering Environments Used
* **Microsoft Excel (Advanced Formulas & Analytics engine):** Utilized for text dimension cleaning parsing, multi-criteria nested conditional checks, dynamic array lookups (COUNTIFS, SUMIFS, AVERAGEIFS), and Pivot Table engineering.
* **Markdown:** Used to generate auditable system logic document summaries (cleaning_log.md and README.md).

---

## 4. Cleaning Steps Performed
1. **Deduplication Audit:** Executed full row matrix scans across all 21 parameters to remove exact carbon-copy rows, recording the exact deletion metric.
2. **Date Re-Serialization:** Isolated fragmented text configurations (e.g., 21 Jul 2024, 08/31/2024) in order_date and ship_date and parsed them into standardized serial date records using the Text-to-Columns wizard.
3. **Missing Value Null Imputation:** Filtered and replaced empty fields in region and ship_mode with a standardized placeholder string ("Unknown").
4. **Calculated Field Generation:** Engineered 8 specialized mathematical columns to establish transaction integrity:
   * cleaned_discount: Replaced blank entries with 0.
   * calculated_sales: quantity * unit_price * (1 - cleaned_discount).
   * calculated_profit: calculated_sales - cost.
   * profit_margin: calculated_profit / calculated_sales (with error safeguards).
   * shipping_delay_days: ship_date - order_date.
   * order_month & order_year: Chronological extractions via =MONTH() and =YEAR().
   * data_quality_flag: Master nested evaluation identifier.

---

## 5. Business Rules Applied
* **Missing Structural Strings:** Empty regions and ship modes were filled as "Unknown" and logged.
* **Discount Bound Verification:** Negative values and values higher than a 70% threshold were caught and assigned an "Invalid" flag.
* **Chronological Sequence Check:** Highlighted and flagged any record where ship_date < order_date as an "Invalid shipping record".
* **Revenue Calculation Sanity:** Evaluated calculated sales against raw ledger sales; variances exceeding $0.01 were marked for financial audit.
* **Completed Revenue Filtering:** Hardcoded performance charts via =SUMIFS() to ensure Cancelled and Failed transactions were completely isolated from completed operational totals.

---

## 6. Summary of Data Quality Issues Found
All identified database anomalies are cleanly partitioned into **outputs/data_quality_report.xlsx** across the following focused worksheets:
* **Missing Value Sheet:** Counts missing fields converted to "Unknown" and empty discounts defaulted to 0.
* **Duplicate Tracker Sheet:** Isolates exact purged records versus overlapping, conflicting order_id values preserved for manual review.
* **Invalid Discount Sheet:** pinpoints negative percentages and extreme discounts exceeding compliance ranges.
* **Date Sequence Tracking Sheet:** Isolates rows breaking real-world timelines (products shipped before order placement).
* **Calculation Mismatch Summary:** Identifies systemic errors where calculated metrics deviate from original raw ledger values.

---

## 7. Summary of Final Pivot Reports
The **outputs/pivot_summary.xlsx** workbook compiles six isolated corporate performance viewpoints:
1. **Sales & Profit by Region:** High-level summary of territorial profit generation.
2. **Sales & Profit by Category/Sub-Category:** Deep-dive inventory view, explicitly sorted descending by top-performing product revenue lines.
3. **Order Count by Ship Mode:** Clear logistical distribution map indicating client delivery channel preferences.
4. **Profit Margin by Customer Segment:** Evaluates average segment profitability using mean calculations.
5. **Exception Defect Matrix by Region:** A filtered analysis targeting only Returned, Cancelled, or Failed statuses to spotlight regional leakage.
6. **Monthly Sales Trend:** Chronological grouping illustrating year-over-year corporate patterns.

---

## 8. Key Business Insights
* **Revenue Leakage Spots:** Cross-referencing regional metrics against the filtered defect matrix shows whether certain territories experience unusually high rates of Failed Payments or Returned Orders.
* **Profit Margin Optimization:** Analyzing the segment profit margins reveals which client clusters yield the highest bottom-line efficiency versus raw volume.
* **Logistics Performance Analysis:** Isolating shipping_delay_days across various fulfillment paths highlights logistics delays and helps identify lanes where delivery targets are missed.

---

## 9. Core Assumptions and Limitations
* **Missing Discount Treatment:** Assumed all blank discount values represented un-discounted, full-price retail transactions (0).
* **Conflict Retention Policy:** Conflicting duplicate order IDs were kept in the sheet intentionally under the assumption they represent split-shipments or system logging collisons.
* **Systemic Constraints:** The pipeline isolates and flags sequence anomalies but cannot retroactively correct transactional errors at the enterprise resource logging source.

---

## 10. Screenshots
*(To make your submission complete, paste screenshots of your core dashboard layouts and final data quality sheets below!)*

### Data Quality Dashboard Overview
![Data Quality Dashboard Placeholder](https://via.placeholder.com/800x400.png?text=Data+Quality+Report+Workbook+Screenshot)

### Core Pivot Summary Workbook
![Pivot Summary Dashboard Placeholder](https://via.placeholder.com/800x400.png?text=Pivot+Summary+Workbook+Screenshot)
