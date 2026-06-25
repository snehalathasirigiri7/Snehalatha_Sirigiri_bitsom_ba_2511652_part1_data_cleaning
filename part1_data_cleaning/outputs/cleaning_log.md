# Data Cleaning and Validation Log
**Project:** Order Data Optimization and Auditing Pipeline
**Data Source:** raw_orders.xlsx
**Output File:** cleaned_orders.xlsx
**Data Quality Report:** data_quality_report.xlsx

---

## 1. Executive Summary & Data Scope
This log provides a comprehensive technical audit trail of the data cleaning, standardization, and rule-application process executed on the raw order transaction database. The primary objective was to transform data anomalies, text mismatches, and logical structural gaps into an enterprise-ready dataset without altering original historical files silently.

---

## 2. Comprehensive List of Issues Found
During the initial data profiling stage, several critical data quality anomalies were identified across multiple columns:
* **Structural Duplication:** Exact duplicate rows were present where identical transactional snapshots were recorded.
* **ID Integrity Failures:** Conflicting records sharing identical order_id values but possessing completely different transactional data metrics (such as customer names or sales figures).
* **Text Formatting & Date Anarchisms:** Date fields (order_date, ship_date) were highly fragmented across multiple text patterns (e.g., DD-MM-YYYY, MM/DD/YYYY, DD MMM YYYY), leading to raw string treatment by database engines.
* **Missing Categorical Data:** Blank/null values discovered inside critical operational columns like region and ship_mode.
* **Financial Deviations:** Missing values within the discount field, negative discount entries (< 0%), and excessive promotional discount percentages (> 70%).
* **Chronological Inversions:** Instances where ship_date historically took place prior to the initialization of the order_date.
* **Transactional Incompletions:** Orders featuring Cancelled status, Failed payment confirmations, or documented product Returned (refunded) events.

---

## 3. Cleaning Actions Performed
The following sequential data cleansing mechanisms were rigorously applied inside Excel to establish standard structure:
1. **Exact Duplicate Purge:** Selected the total active block area and executed a rigid *Remove Duplicates* filter across all columns. 
2. **Text-to-Columns Chronological Realignment:** Forced stubborn string configurations within order_date and ship_date columns into explicit unified serial number date dimensions using the delimited parsing wizard.
3. **Missing Value Injector:** Isolated empty/blank categorical records via automated string replacements, mapping empty strings to designated placeholders to ensure complete vector calculations.

---

## 4. Business Rules Applied & Formula Mapping
To maintain a strict, auditable governance model, the following business validation matrix rules were hardcoded using nested logic fields to determine the data_quality_flag:

### A. Categorical Rules
* **Missing Region:** Populated empty records with "Unknown" and flagged the data row as "Missing region".
* **Missing Ship Mode:** Populated empty records with "Unknown" and flagged the data row as "Missing ship_mode".

### B. Financial Metrics Rules
* **Missing Discounts:** Treated as 0 under the conditional assumption that no markdown was applied, provided financial metrics were sound.
* **Out-of-Bounds Discounts:** Flags negative numbers (< 0) and extreme values exceeding a maximum corporate threshold (> 0.70 / 70%) as "Invalid".
* **Sales/Profit Calculations:** Re-computed true transactions mathematically. If the absolute variance between the raw metrics and the newly engineered formulas (calculated_sales = quantity * unit_price * (1 - discount)) exceeded a $0.01 threshold, rows were logged within the mismatch summary sheet.

### C. Logistics & Operational Rules
* **Date Sequence Inversions:** Verified chronological timelines. Rows where ship_date < order_date were labeled as "Invalid shipping record".
* **Completed-Sales Summaries Filter:** Explicitly isolated Cancelled orders and Failed payments from the performance summary aggregates using strict =SUMIFS() matrix limits (order_status = "Completed", payment_status = "Paid").
* **Refund Multi-Bucket Mapping:** Isolated Returned orders into a distinct performance dashboard bucket to avoid skewing completed baseline trends.

---

## 5. Core Assumptions Made
* **Chronological Reality:** It is operationally impossible for an item to physically ship to a customer before the customer places the transaction order.
* **Discount Absence:** A blank or missing value within the promotional discount matrix field is interpreted as a zero percent (0.00) markdown applied.
* **Data Permanence:** Conflicting order_id values represent tracking collisions or multi-item splits rather than simple typographical errors; therefore, they must be preserved for compliance and stakeholder inspection rather than dropped silently.

---

## 6. Audit Volume: Records Removed vs. Flagged
* **Records Removed (Exact Duplicates):** [Insert the exact count of rows Excel deleted during the Remove Duplicates step] records were permanently scrubbed from the main row matrix.
* **Records Flagged (Retained for Audit Review):** All remaining rows failing data-quality parameters were caught by the master nested IF column and logged in outputs/data_quality_report.xlsx under categories such as:
  * *Conflicting Duplicate Order ID*
  * *Missing region / Missing ship_mode*
  * *Invalid shipping record*
  * *Invalid (Discount errors)*

---

## 7. Limitations of the Cleaning Process
While the applied data pipeline resolves all structural text formatting variance and surface logical rule violations, certain limitations remain:
* **No Root Cause Correction:** Flagging structural errors like an item shipping before it was ordered fixes the reporting calculation, but it cannot fix underlying systemic enterprise resource planning (ERP) logging delays.
* **Manual Threshold Dependence:** The maximum discount limit (70%) is an assumed business heuristic. Legitimate high-volume corporate clearances matching this profile are flagged indiscriminately as "Invalid" and require manual administrative overrides.
* **Address/Customer Data Blindspot:** The pipeline checks structural order parameters but assumes textual names, locations, and ID keys are spelled correctly unless completely empty.
