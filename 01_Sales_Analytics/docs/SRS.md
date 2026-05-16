# Software Requirements Specification (SRS)
## Project 1 — Sales Analytics Dashboard
### DATA_ANALYTICS_POWERBI | Anshul-RA1

---

**Document Version:** 1.0  
**Created:** May 2026  
**Author:** Anshul Raghuvanshi  
**GitHub:** github.com/Anshul-RA1/DATA_ANALYTICS_POWERBI  
**Status:** Production Ready  

---

## Table of Contents

1. Introduction
2. Project Overview
3. Stakeholders
4. Business Requirements
5. Functional Requirements
6. Non-Functional Requirements
7. Data Requirements
8. Report Requirements
9. Security Requirements
10. Constraints and Assumptions

---

## 1. Introduction

### 1.1 Purpose
This Software Requirements Specification (SRS) document defines the complete requirements for the Sales Analytics Dashboard — Project 1 of the DATA_ANALYTICS_POWERBI portfolio. It serves as the reference document for development, testing, and future maintenance of the Power BI solution.

### 1.2 Scope
The Sales Analytics Dashboard is a production-grade Power BI report built on the Sample Superstore dataset. It provides end-to-end sales performance visibility across products, customers, regions, and time periods. The solution covers data ingestion, transformation, modeling, DAX calculations, report visualization, and row-level security.

### 1.3 Definitions and Abbreviations

| Term | Definition |
|------|------------|
| DAX | Data Analysis Expressions — formula language used in Power BI |
| ETL | Extract, Transform, Load — data preparation process |
| KPI | Key Performance Indicator — a measurable business metric |
| MTD | Month to Date — cumulative value from 1st of current month |
| QTD | Quarter to Date — cumulative value from 1st of current quarter |
| YTD | Year to Date — cumulative value from January 1st |
| YoY | Year over Year — comparison of current period vs same period last year |
| MoM | Month over Month — comparison of current month vs previous month |
| RLS | Row Level Security — data access restriction by user role |
| Star Schema | Data modeling pattern with one fact table and multiple dimension tables |
| PBIP | Power BI Project format — Git-friendly file format |
| FK | Foreign Key — column linking to primary key in another table |
| PK | Primary Key — unique identifier column in a dimension table |
| SRS | Software Requirements Specification — this document |
| LLD | Low Level Design — technical design document |
| UAT | User Acceptance Testing — testing by end users |

### 1.4 References
- Sample Superstore Dataset (Excel) — 9,994 rows × 21 columns
- Power BI Desktop April 2026 (v2.153.1206.0 64-bit)
- GitHub Repository: github.com/Anshul-RA1/DATA_ANALYTICS_POWERBI

---

## 2. Project Overview

### 2.1 Background
The Sample Superstore dataset represents a fictional US-based retail company selling office supplies, furniture, and technology products across four regions (West, East, Central, South) from 2014 to 2017. The business requires a comprehensive analytics solution to understand sales performance, profitability, customer behavior, and time-based trends.

### 2.2 Objectives
- Build a production-grade Power BI dashboard for sales performance analysis
- Demonstrate end-to-end BI development skills for portfolio and interview purposes
- Implement professional data modeling using Star Schema
- Create 27 DAX measures covering basic to advanced calculations
- Build 5 interactive report pages with drill-through capability
- Implement Row Level Security (RLS) for regional data access control
- Follow professional Git workflow with branch protection and PR reviews

### 2.3 Expected Outcomes
- Fully functional 5-page Power BI report
- Star Schema data model with 5 tables
- 27 production-grade DAX measures
- RLS with 4 regional roles
- Complete documentation suite
- GitHub repository with professional commit history

---

## 3. Stakeholders

| Stakeholder | Role | Interest |
|-------------|------|----------|
| Anshul Raghuvanshi | Developer / Analyst | Build and maintain the solution |
| Sales Manager | Primary End User | Monitor sales KPIs and trends |
| Regional Manager (West) | End User | View West region data only (RLS) |
| Regional Manager (East) | End User | View East region data only (RLS) |
| Regional Manager (Central) | End User | View Central region data only (RLS) |
| Regional Manager (South) | End User | View South region data only (RLS) |
| Executive Leadership | End User | High-level KPI overview |
| Data Engineer | Technical | Data pipeline and refresh |
| Hiring Manager | Reviewer | Portfolio evaluation |

---

## 4. Business Requirements

### 4.1 Executive Overview
- BR-001: System shall display total sales revenue across all time periods
- BR-002: System shall display total profit and profit margin percentage
- BR-003: System shall display total unique orders and average order value
- BR-004: System shall show monthly sales trend across all years
- BR-005: System shall show sales breakdown by product category
- BR-006: System shall show geographic sales distribution by region

### 4.2 Product Performance
- BR-007: System shall identify top 10 best-selling products by revenue
- BR-008: System shall show sales breakdown by category and sub-category
- BR-009: System shall allow filtering by year and product category

### 4.3 Customer Insights
- BR-010: System shall identify top 10 customers by revenue
- BR-011: System shall show customer segment distribution
- BR-012: System shall show regional sales performance comparison

### 4.4 Time Intelligence
- BR-013: System shall calculate Month to Date (MTD) sales
- BR-014: System shall calculate Quarter to Date (QTD) sales
- BR-015: System shall calculate Year to Date (YTD) sales
- BR-016: System shall calculate Year over Year (YoY) growth percentage
- BR-017: System shall calculate Month over Month (MoM) growth percentage
- BR-018: System shall provide year × month sales matrix

### 4.5 Security
- BR-019: Regional managers shall only see data for their assigned region
- BR-020: Four roles required: West, East, Central, South

---

## 5. Functional Requirements

### 5.1 Data Layer

**FR-001 — Data Source Connection**
- Connect to Sample-Superstore.xlsx via Power BI Get Data → Excel
- Load all sheets into Power Query Editor
- Disable RAW_DATA table from loading (staging only)

**FR-002 — Power Query Transformations**
- Promote first row as headers for all tables
- Set correct data types:
  - Order Date, Ship Date → Date
  - Postal Code → Text (not number)
  - Sales, Profit → Decimal Number
  - Quantity → Whole Number
  - Discount → Decimal Number

**FR-003 — Star Schema Tables**

| Table | Type | Rows | Primary Key |
|-------|------|------|-------------|
| fact_Sales | Fact | 9,994 | Row ID |
| dim_Customer | Dimension | 793 | Customer ID |
| dim_Product | Dimension | 1,000 | Product ID |
| dim_Region | Dimension | 631 | Postal Code |
| dim_Date | Dimension | 1,826 | Date |

**FR-004 — dim_Date Table (M Language)**
- Built from scratch using List.Dates() in M Language
- Date range: January 1, 2014 to December 31, 2018
- Columns: Date, Year, Month Number, Month Name, Quarter, Day of Week Name, Is Weekend
- Marked as Date Table in Power BI
- Month Name sorted by Month Number column

**FR-005 — Relationships**

| From (Many side) | To (One side) | Cardinality | Direction |
|------------------|---------------|-------------|-----------|
| fact_Sales[Order Date] | dim_Date[Date] | Many:1 | Single |
| fact_Sales[Customer ID] | dim_Customer[Customer ID] | Many:1 | Single |
| fact_Sales[Product ID] | dim_Product[Product ID] | Many:1 | Single |
| fact_Sales[Postal Code] | dim_Region[Postal Code] | Many:1 | Single |

**FR-006 — Hidden FK Columns**
- Customer ID hidden from report view in fact_Sales
- Product ID hidden from report view in fact_Sales
- Postal Code hidden from report view in fact_Sales
- Row ID hidden from report view in fact_Sales

### 5.2 DAX Measures

**FR-007 — Measures Table Setup**
- Table name: _Measures (underscore = sorts to top)
- Placeholder column: dmmy
- All 27 measures stored here

**FR-008 — Basic Aggregations (9 measures)**

| # | Measure | Formula | Format |
|---|---------|---------|--------|
| 1 | Total Sales | SUM(fact_Sales[Sales]) | $ |
| 2 | Total Profit | SUM(fact_Sales[Profit]) | $ |
| 3 | Total Orders | DISTINCTCOUNT(fact_Sales[Order ID]) | Whole |
| 4 | Total Quantity | SUM(fact_Sales[Quantity]) | Whole |
| 5 | Profit Margin % | DIVIDE([Total Profit],[Total Sales],0) | % |
| 6 | Average Order Value | DIVIDE([Total Sales],[Total Orders],0) | $ |
| 7 | West Sales | CALCULATE([Total Sales],dim_Region[Region]="West") | $ |
| 8 | Sales % of Total | DIVIDE([Total Sales],CALCULATE([Total Sales],ALL(fact_Sales)),0) | % |
| 9 | Profitable Orders | CALCULATE([Total Orders],fact_Sales[Profit]>0) | Whole |

**FR-009 — Time Intelligence (9 measures)**

| # | Measure | Formula | Format |
|---|---------|---------|--------|
| 10 | Sales MTD | TOTALMTD([Total Sales],dim_Date[Date]) | $ |
| 11 | Sales QTD | TOTALQTD([Total Sales],dim_Date[Date]) | $ |
| 12 | Sales YTD | TOTALYTD([Total Sales],dim_Date[Date]) | $ |
| 13 | Sales PY | CALCULATE([Total Sales],SAMEPERIODLASTYEAR(dim_Date[Date])) | $ |
| 14 | Sales PM | CALCULATE([Total Sales],DATEADD(dim_Date[Date],-1,MONTH)) | $ |
| 15 | Sales PQ | CALCULATE([Total Sales],DATEADD(dim_Date[Date],-1,QUARTER)) | $ |
| 16 | YoY Growth | [Total Sales]-[Sales PY] | $ |
| 17 | YoY Growth % | DIVIDE([YoY Growth],[Sales PY],0) | % |
| 18 | MoM Growth % | DIVIDE([Total Sales]-[Sales PM],[Sales PM],0) | % |

**FR-010 — Iterator Functions (2 measures)**

| # | Measure | Formula | Format |
|---|---------|---------|--------|
| 19 | Average Selling Price | DIVIDE(SUMX(fact_Sales,Sales*Quantity),SUM(Quantity),0) | $ |
| 20 | Avg Discount % | AVERAGEX(fact_Sales,fact_Sales[Discount]) | % |

**FR-011 — Ranking (3 measures)**

| # | Measure | Formula | Format |
|---|---------|---------|--------|
| 21 | Product Rank | RANKX(ALL(dim_Product),[Total Sales],,DESC,DENSE) | Whole |
| 22 | Customer Rank | RANKX(ALL(dim_Customer),[Total Sales],,DESC,DENSE) | Whole |
| 23 | Is Top 10 Product | IF([Product Rank]<=10,"Top 10","Others") | Text |

**FR-012 — VAR/RETURN (2 measures)**

| # | Measure | Tiers | Format |
|---|---------|-------|--------|
| 24 | Sales Performance Status | Excellent/Growing/Declining/Critical | Text |
| 25 | Profit Category | High/Medium/Low/Loss Making | Text |

**FR-013 — Advanced (2 measures)**

| # | Measure | Formula | Format |
|---|---------|---------|--------|
| 26 | Running Total Sales | CALCULATE([Total Sales],FILTER(ALL(dim_Date),Date<=MAX(Date))) | $ |
| 27 | Dynamic Title | "Sales Analysis — " & SELECTEDVALUE(dim_Date[Year],"All Years") | Text |

### 5.3 Report Pages

**FR-014 — Page 1: Executive Overview**
- Slicers: Year (Dropdown), Region (Dropdown)
- KPI Cards: Total Sales ($2.3M), Total Profit ($286K), Profit Margin % (12.47%), Total Orders (5,009), Average Order Value ($458.61)
- Line Chart: Monthly Sales Trend (X=Month Name, Y=Total Sales, Legend=Year, sorted by Month Number)
- Bar Chart: Sales by Category (Technology > Furniture > Office Supplies)
- Treemap: Sales by Region (Map disabled — tenant restriction, Treemap used instead)
- Title: "Sales Analytics Dashboard"

**FR-015 — Page 2: Product Performance**
- Slicers: Year (Dropdown), Category (Dropdown)
- Bar Chart: Top 10 Products by Sales (Top N filter applied)
- Donut Chart: Sales by Category
- Matrix: Category × Sub-Category with conditional formatting
- Title: "Product Performance"

**FR-016 — Page 3: Customer Insights**
- Slicers: Year (Dropdown), Segment (Dropdown)
- Table: Top 10 Customers (Top N filter, columns: Name, Segment, Sales, Profit, Rank)
- Donut Chart: Sales by Segment (Consumer, Corporate, Home Office)
- Bar Chart: Sales by Region (West highest, South lowest)
- Title: "Customer Insights"

**FR-017 — Page 4: Time Analysis**
- Slicer: Year (Dropdown)
- KPI Cards: Sales MTD, Sales QTD, Sales YTD, YoY Growth %, MoM Growth %
- Line Chart: Current Year vs Previous Year (YoY comparison)
- Column Chart: Month over Month Growth %
- Matrix: Year × Month Sales with conditional formatting
- Title: "Time Analysis"
- Note: Select Year 2017 to see populated Time Intelligence values

**FR-018 — Page 5: Order Details (Drill-through)**
- Drill-through field: dim_Product[Category]
- Table: Order ID, Order Date, Product Name, Customer Name, Region, Sales, Profit
- Back button for navigation
- Title: "Order Details"

### 5.4 Security

**FR-019 — RLS Implementation**

| Role Name | Table | DAX Filter | Verified |
|-----------|-------|-----------|---------|
| West_Region | dim_Region | [Region] = "West" | ✅ |
| East_Region | dim_Region | [Region] = "East" | ✅ |
| Central_Region | dim_Region | [Region] = "Central" | ✅ |
| South_Region | dim_Region | [Region] = "South" | ✅ |

---

## 6. Non-Functional Requirements

| ID | Requirement | Category |
|----|------------|----------|
| NFR-001 | Report loads within 10 seconds | Performance |
| NFR-002 | Slicer interactions respond within 3 seconds | Performance |
| NFR-003 | VAR/RETURN pattern used in complex measures | Performance |
| NFR-004 | Consistent dark theme across all pages | Usability |
| NFR-005 | All visuals have descriptive titles | Usability |
| NFR-006 | Dropdown style slicers for space efficiency | Usability |
| NFR-007 | PBIP format for Git compatibility | Maintainability |
| NFR-008 | Branch protection on main (PR required) | Maintainability |
| NFR-009 | DIVIDE() used instead of / operator | Reliability |
| NFR-010 | Feature branch workflow enforced | Maintainability |

---

## 7. Data Requirements

### 7.1 Source Data Profile

| Attribute | Value |
|-----------|-------|
| File Name | Sample-Superstore.xlsx |
| Total Rows | 9,994 order line items |
| Total Columns | 21 |
| Date Range | January 3, 2014 — December 30, 2017 |
| Geography | United States only |
| Regions | West, East, Central, South |
| Categories | Furniture, Office Supplies, Technology |
| Unique Customers | 793 |
| Unique Products | 1,000 |
| Unique Orders | 5,009 |
| Total Revenue | $2,297,200.86 |
| Total Profit | $286,397.02 |

### 7.2 Data Quality Rules
- No duplicate Row IDs
- Order Date must precede Ship Date
- Sales must be greater than 0
- Discount must be between 0 and 1
- Postal Code stored as Text

### 7.3 Git Exclusions (.gitignore)
- *.xlsx, *.xls (source data files)
- *.csv (intermediate data files)
- Lock files

---

## 8. Report Theme

| Property | Value |
|----------|-------|
| Theme File | Anshul_RA1_Theme_v2.json |
| Location | 01_Sales_Analytics/templates/ |
| Background | #1E2130 (Dark Navy) |
| Visual Background | #252B3B |
| Primary Accent | #4FACFE (Blue) |
| Positive Color | #68D391 (Green) |
| Negative Color | #FC8181 (Red) |
| Primary Font | Segoe UI |
| KPI Font Size | 28pt |
| Title Font Size | 20pt |

---

## 9. Security Requirements

| ID | Requirement |
|----|------------|
| SR-001 | RLS implemented at dim_Region table level |
| SR-002 | Four roles for four geographic regions |
| SR-003 | All roles tested using View as Role |
| SR-004 | Source Excel excluded from Git via .gitignore |

---

## 10. Constraints and Assumptions

### 10.1 Constraints
- Map visual disabled (Microsoft tenant admin restriction — Treemap used instead)
- Power BI Service deployment deferred (personal account limitation)
- Time Intelligence returns BLANK when system date (2026) has no historical data
- Dataset limited to 2014-2017

### 10.2 Out of Scope
- Real-time data connectivity
- Power BI Service deployment and scheduled refresh
- Mobile layout optimization
- Automated ETL pipeline

---

*Anshul-RA1 | DATA_ANALYTICS_POWERBI | Project 1 — Sales Analytics*
*SRS Version 1.0 | May 2026*
