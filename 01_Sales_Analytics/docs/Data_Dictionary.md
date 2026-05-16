# Data Dictionary
## Project 1 — Sales Analytics Dashboard
### DATA_ANALYTICS_POWERBI | Anshul-RA1

---

**Document Version:** 1.0  
**Created:** May 2026  
**Author:** Anshul Raghuvanshi  
**Status:** Production Ready  

---

## Table of Contents

1. Source Data Overview
2. fact_Sales
3. dim_Customer
4. dim_Product
5. dim_Region
6. dim_Date
7. _Measures Table
8. DAX Measures Reference

---

## 1. Source Data Overview

| Attribute | Value |
|-----------|-------|
| Source File | Sample-Superstore.xlsx |
| Total Rows | 9,994 |
| Total Columns | 21 |
| Date Range | January 3, 2014 — December 30, 2017 |
| Geography | United States |
| Currency | USD |
| Categories | Furniture, Office Supplies, Technology |
| Regions | West, East, Central, South |

---

## 2. fact_Sales

**Description:** Central fact table containing all sales transaction line items. One row = one product line item within an order. An order can have multiple rows.

**Source:** RAW_DATA → Reference query → fact_Sales  
**Rows:** 9,994  
**Primary Key:** Row ID  

| Column Name | Data Type | Description | Example | Notes |
|-------------|-----------|-------------|---------|-------|
| Row ID | Whole Number | Unique identifier for each line item | 1, 2, 3 | Primary Key |
| Order ID | Text | Unique order identifier | CA-2016-152156 | Multiple rows per order possible |
| Order Date | Date | Date when order was placed | 2016-11-08 | FK → dim_Date[Date] |
| Ship Date | Date | Date when order was shipped | 2016-11-11 | Always after Order Date |
| Ship Mode | Text | Shipping method selected | Second Class | First Class, Second Class, Standard Class, Same Day |
| Customer ID | Text | Unique customer identifier | CG-12520 | FK → dim_Customer[Customer ID] — Hidden |
| Product ID | Text | Unique product identifier | FUR-BO-10001798 | FK → dim_Product[Product ID] — Hidden |
| Postal Code | Text | Delivery postal code | 42420 | FK → dim_Region[Postal Code] — Hidden, stored as Text |
| Sales | Decimal Number | Revenue from this line item | 261.96 | Always positive |
| Quantity | Whole Number | Units sold in this line item | 2 | Always positive integer |
| Discount | Decimal Number | Discount applied (0 to 1) | 0.00 | 0 = no discount, 0.5 = 50% off |
| Profit | Decimal Number | Profit from this line item | 41.91 | Can be negative (loss) |

**Business Rules:**
- One Order ID can appear across multiple rows (one per product in the order)
- Profit = Sales - Cost - Discount effect
- Discount of 0.4 or above often results in negative profit
- Ship Date is always equal to or after Order Date

---

## 3. dim_Customer

**Description:** Customer dimension table containing unique customer information. Created from RAW_DATA via Reference query with deduplication.

**Source:** RAW_DATA → Reference query → dim_Customer  
**Rows:** 793 unique customers  
**Primary Key:** Customer ID  

| Column Name | Data Type | Description | Example | Notes |
|-------------|-----------|-------------|---------|-------|
| Customer ID | Text | Unique customer identifier | CG-12520 | Primary Key |
| Customer Name | Text | Full name of customer | Claire Gute | Fictional names |
| Segment | Text | Business segment of customer | Consumer | Consumer, Corporate, Home Office |

**Segment Definitions:**

| Segment | Description | Typical Buyer |
|---------|-------------|---------------|
| Consumer | Individual retail customers | Personal purchases |
| Corporate | Business customers | Company purchases |
| Home Office | Small business / home office | Mixed personal-business |

---

## 4. dim_Product

**Description:** Product dimension containing unique product catalog with three-level hierarchy: Category → Sub-Category → Product Name.

**Source:** RAW_DATA → Reference query → dim_Product  
**Rows:** 1,000 unique products  
**Primary Key:** Product ID  

| Column Name | Data Type | Description | Example | Notes |
|-------------|-----------|-------------|---------|-------|
| Product ID | Text | Unique product identifier | FUR-BO-10001798 | Primary Key. Format: CAT-SUB-NUMBER |
| Category | Text | Top-level product category | Furniture | 3 categories |
| Sub-Category | Text | Mid-level product grouping | Bookcases | 17 sub-categories |
| Product Name | Text | Full product name | Bush Somerset Collection Bookcase | Specific product |

**Category Hierarchy:**

| Category | Sub-Categories |
|----------|----------------|
| Furniture | Bookcases, Chairs, Furnishings, Tables |
| Office Supplies | Appliances, Art, Binders, Envelopes, Fasteners, Labels, Paper, Storage, Supplies |
| Technology | Accessories, Copiers, Machines, Phones |

---

## 5. dim_Region

**Description:** Geographic dimension containing unique location information. Postal Code is the primary key linking to sales transactions.

**Source:** RAW_DATA → Reference query → dim_Region  
**Rows:** 631 unique postal codes  
**Primary Key:** Postal Code  

| Column Name | Data Type | Description | Example | Notes |
|-------------|-----------|-------------|---------|-------|
| Postal Code | Text | US postal code | 42420 | Primary Key. Text type to preserve leading zeros |
| City | Text | City name | Henderson | US city |
| State | Text | US state name | Kentucky | Full state name |
| Country | Text | Country | United States | Always United States |
| Region | Text | Geographic sales region | South | West, East, Central, South. RLS filter column |

**Region Definitions:**

| Region | States Included | Sales Share |
|--------|----------------|-------------|
| West | California, Washington, Oregon, Nevada, etc. | ~31.5% (highest) |
| East | New York, Pennsylvania, Ohio, etc. | ~29.4% |
| Central | Texas, Illinois, Michigan, etc. | ~21.8% |
| South | Florida, North Carolina, Georgia, etc. | ~17.0% (lowest) |

---

## 6. dim_Date

**Description:** Calendar date dimension built entirely from scratch using M Language (Power Query). Contains all dates from January 1, 2014 to December 31, 2018 — covering the full data range plus one year forward for forecasting.

**Source:** Built using List.Dates() in M Language  
**Rows:** 1,826 calendar days  
**Primary Key:** Date  
**Marked as Date Table:** Yes (required for Time Intelligence)  

| Column Name | Data Type | Description | Example | Notes |
|-------------|-----------|-------------|---------|-------|
| Date | Date | Calendar date | 2016-11-08 | Primary Key. Marked as Date Table |
| Year | Whole Number | Calendar year | 2016 | 2014, 2015, 2016, 2017, 2018 |
| Month Number | Whole Number | Month as number (1-12) | 11 | Used to sort Month Name correctly |
| Month Name | Text | Full month name | November | Sorted by Month Number column |
| Quarter | Text | Quarter label | Q4 | Q1, Q2, Q3, Q4 |
| Day of Week Name | Text | Day name | Tuesday | Monday through Sunday |
| Is Weekend | Logical | True if Saturday or Sunday | false | DayOfWeek >= 5 = weekend |

**Date Range Rationale:**
- Start: 2014-01-01 — covers first order date (2014-01-03)
- End: 2018-12-31 — one year beyond last order (2017-12-30) for future planning
- Continuous dates required — no gaps allowed for Time Intelligence to work

---

## 7. _Measures Table

**Description:** Dedicated measures table created via Enter Data. Contains no actual data — only serves as a container for all 27 DAX measures. The underscore prefix ensures it sorts to the top of the Data pane alphabetically.

**Source:** Enter Data → Empty table  
**Rows:** 1 (placeholder dmmy column)  
**Load Enabled:** Yes (must be enabled to appear in Data pane)  

| Column | Data Type | Description |
|--------|-----------|-------------|
| dmmy | Text | Placeholder column — not used in reports |

---

## 8. DAX Measures Reference

### Category 1 — Basic Aggregations

| # | Measure Name | DAX Formula | Format | Business Definition |
|---|-------------|-------------|--------|---------------------|
| 1 | Total Sales | SUM(fact_Sales[Sales]) | $ Currency | Total revenue across all transactions |
| 2 | Total Profit | SUM(fact_Sales[Profit]) | $ Currency | Total profit including losses |
| 3 | Total Orders | DISTINCTCOUNT(fact_Sales[Order ID]) | Whole Number | Count of unique orders |
| 4 | Total Quantity | SUM(fact_Sales[Quantity]) | Whole Number | Total units sold |
| 5 | Profit Margin % | DIVIDE([Total Profit],[Total Sales],0) | Percentage | Profit as % of revenue |
| 6 | Average Order Value | DIVIDE([Total Sales],[Total Orders],0) | $ Currency | Average revenue per order |
| 7 | West Sales | CALCULATE([Total Sales],dim_Region[Region]="West") | $ Currency | Sales from West region only |
| 8 | Sales % of Total | DIVIDE([Total Sales],CALCULATE([Total Sales],ALL(fact_Sales)),0) | Percentage | Each dimension's % of grand total |
| 9 | Profitable Orders | CALCULATE([Total Orders],fact_Sales[Profit]>0) | Whole Number | Orders with positive profit |

### Category 2 — Time Intelligence

| # | Measure Name | DAX Formula | Format | Business Definition |
|---|-------------|-------------|--------|---------------------|
| 10 | Sales MTD | TOTALMTD([Total Sales],dim_Date[Date]) | $ Currency | Sales from 1st of current month to date |
| 11 | Sales QTD | TOTALQTD([Total Sales],dim_Date[Date]) | $ Currency | Sales from 1st of current quarter to date |
| 12 | Sales YTD | TOTALYTD([Total Sales],dim_Date[Date]) | $ Currency | Sales from Jan 1 to current date |
| 13 | Sales PY | CALCULATE([Total Sales],SAMEPERIODLASTYEAR(dim_Date[Date])) | $ Currency | Same period previous year |
| 14 | Sales PM | CALCULATE([Total Sales],DATEADD(dim_Date[Date],-1,MONTH)) | $ Currency | Previous month sales |
| 15 | Sales PQ | CALCULATE([Total Sales],DATEADD(dim_Date[Date],-1,QUARTER)) | $ Currency | Previous quarter sales |
| 16 | YoY Growth | [Total Sales]-[Sales PY] | $ Currency | Dollar growth vs last year |
| 17 | YoY Growth % | DIVIDE([YoY Growth],[Sales PY],0) | Percentage | Percentage growth vs last year |
| 18 | MoM Growth % | DIVIDE([Total Sales]-[Sales PM],[Sales PM],0) | Percentage | Percentage growth vs last month |

### Category 3 — Iterator Functions

| # | Measure Name | DAX Formula | Format | Business Definition |
|---|-------------|-------------|--------|---------------------|
| 19 | Average Selling Price | DIVIDE(SUMX(fact_Sales,fact_Sales[Sales]*fact_Sales[Quantity]),SUM(fact_Sales[Quantity]),0) | $ Currency | Weighted average price per unit |
| 20 | Avg Discount % | AVERAGEX(fact_Sales,fact_Sales[Discount]) | Percentage | Average discount rate applied |

### Category 4 — Ranking

| # | Measure Name | DAX Formula | Format | Business Definition |
|---|-------------|-------------|--------|---------------------|
| 21 | Product Rank | RANKX(ALL(dim_Product),[Total Sales],,DESC,DENSE) | Whole Number | Rank of product by sales (1=highest) |
| 22 | Customer Rank | RANKX(ALL(dim_Customer),[Total Sales],,DESC,DENSE) | Whole Number | Rank of customer by sales (1=highest) |
| 23 | Is Top 10 Product | IF([Product Rank]<=10,"Top 10","Others") | Text | Flag for top 10 product filtering |

### Category 5 — VAR/RETURN

| # | Measure Name | Logic | Format | Business Definition |
|---|-------------|-------|--------|---------------------|
| 24 | Sales Performance Status | YoY%>=10%: Excellent, >=0%: Growing, >=-10%: Declining, else: Critical | Text | Performance tier based on YoY growth |
| 25 | Profit Category | Margin>=20%: High, >=10%: Medium, >=0%: Low, else: Loss Making | Text | Profitability classification |

### Category 6 — Advanced

| # | Measure Name | DAX Formula | Format | Business Definition |
|---|-------------|-------------|--------|---------------------|
| 26 | Running Total Sales | CALCULATE([Total Sales],FILTER(ALL(dim_Date),dim_Date[Date]<=MAX(dim_Date[Date]))) | $ Currency | Cumulative sales from start of data |
| 27 | Dynamic Title | "Sales Analysis — " & SELECTEDVALUE(dim_Date[Year],"All Years") | Text | Context-aware report title |

---

## Business Metrics Summary

| Metric | Value | Period |
|--------|-------|--------|
| Total Revenue | $2,297,200.86 | 2014-2017 |
| Total Profit | $286,397.02 | 2014-2017 |
| Profit Margin | 12.47% | 2014-2017 |
| Total Orders | 5,009 | 2014-2017 |
| Total Units | ~38,000 | 2014-2017 |
| Average Order Value | $458.61 | 2014-2017 |
| Best Region | West | ~$725K |
| Best Category | Technology | ~$836K |
| YoY Growth 2017 | 20.36% | 2017 vs 2016 |

---

*Anshul-RA1 | DATA_ANALYTICS_POWERBI | Project 1 — Sales Analytics*
*Data Dictionary Version 1.0 | May 2026*
