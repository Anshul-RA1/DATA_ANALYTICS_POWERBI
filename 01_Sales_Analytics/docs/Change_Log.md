# Change Log
## Project 1 — Sales Analytics Dashboard
### DATA_ANALYTICS_POWERBI | Anshul-RA1

---

**Document Version:** 1.0  
**Created:** May 2026  
**Author:** Anshul Raghuvanshi  
**GitHub:** github.com/Anshul-RA1/DATA_ANALYTICS_POWERBI  

---

## Version History

---

## v1.0.0 — May 2026 — PRODUCTION RELEASE

**Branch:** main  
**Status:** ✅ Production Ready  
**Total Commits:** 12+  

---

### Phase 0 — Environment Setup

**Commit:** `feat: initial project setup and folder structure`  
**Branch:** feature/* → develop → main  

**Changes:**
- Created GitHub repository: DATA_ANALYTICS_POWERBI (Public)
- Created branch protection ruleset: protect-main
  - PR required before merging to main
  - Direct push blocked
- Created folder structure:
  ```
  01_Sales_Analytics/data/
  01_Sales_Analytics/pbip/
  01_Sales_Analytics/docs/
  01_Sales_Analytics/dax/
  01_Sales_Analytics/screenshots/
  01_Sales_Analytics/templates/
  02_HR_Analytics/ (placeholder)
  03_Financial_Reporting/ (placeholder)
  templates/ (shared)
  ```
- Added .gitkeep files (16 total) for empty directories
- Created .gitignore (excludes *.xlsx, *.xls, *.csv, lock files)
- Enabled PBIP format in Power BI Desktop Options
- Installed Power BI Desktop April 2026 (v2.153.1206.0 64-bit)

---

### Phase 1 — Data Connection

**Commit:** `feat(phase1): connect superstore dataset, configure Power Query`  
**Branch:** feature/phase1-data-connection → develop → main  

**Changes:**
- Connected Sample-Superstore.xlsx via Get Data → Excel
- Opened Power Query Editor
- Applied transformations to RAW_DATA:
  - Promoted headers
  - Fixed data types:
    - Order Date, Ship Date → Date
    - Postal Code → Text (prevents leading zero loss)
    - Sales, Profit → Decimal Number
    - Quantity → Whole Number
    - Discount → Decimal Number
- Saved report in PBIP format: Sales_Analytics_PBI.pbip

---

### Phase 2 — Power Query (ETL)

**Commit:** `feat(phase2-3): star schema tables, relationships, date table, FK hidden`  
**Branch:** feature/phase1-data-connection → develop  

**Changes:**

**dim_Customer created:**
- Reference from RAW_DATA
- Columns kept: Customer ID, Customer Name, Segment
- Removed duplicates by Customer ID
- Result: 793 unique customers

**dim_Product created:**
- Reference from RAW_DATA
- Columns kept: Product ID, Category, Sub-Category, Product Name
- Removed duplicates by Product ID
- Result: 1,000 unique products

**dim_Region created:**
- Reference from RAW_DATA
- Columns kept: Postal Code, City, State, Country, Region
- Removed duplicates by Postal Code
- Result: 631 unique locations

**fact_Sales created:**
- Reference from RAW_DATA
- Columns kept: Row ID, Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Product ID, Postal Code, Sales, Quantity, Discount, Profit
- Result: 9,994 transaction rows

**dim_Date created from scratch:**
- Built using M Language (List.Dates function)
- Date range: 2014-01-01 to 2018-12-31
- Columns added: Date, Year, Month Number, Month Name, Quarter, Day of Week Name, Is Weekend
- Result: 1,826 calendar days

**RAW_DATA:**
- Load disabled (staging only, not loaded to model)

---

### Phase 3 — Data Modeling

**Commit:** (included in phase2-3 commit)

**Changes:**

**Relationships created (4 total):**
- dim_Date[Date] → fact_Sales[Order Date] (1:Many, Single direction, Active)
- dim_Customer[Customer ID] → fact_Sales[Customer ID] (1:Many, Single direction, Active)
- dim_Product[Product ID] → fact_Sales[Product ID] (1:Many, Single direction, Active)
- dim_Region[Postal Code] → fact_Sales[Postal Code] (1:Many, Single direction, Active)

**dim_Date marked as Date Table:**
- Enables Time Intelligence functions (TOTALYTD, SAMEPERIODLASTYEAR, DATEADD etc.)

**FK columns hidden from report view:**
- fact_Sales[Customer ID] — hidden
- fact_Sales[Product ID] — hidden
- fact_Sales[Postal Code] — hidden
- fact_Sales[Row ID] — hidden

**Month Name sorting:**
- dim_Date[Month Name] sorted by dim_Date[Month Number]
- Ensures chronological order in visuals (January → December)

---

### Phase 4 — DAX Measures

**Commit:** `feat(phase4): 27 DAX measures — basic, time intelligence, iterators, ranking, VAR/RETURN, advanced`  
**Branch:** feature/phase4-dax-measures → develop → main  

**Changes:**

**_Measures table created:**
- Via Enter Data
- Name: _Measures (underscore for top sorting)
- Placeholder column: dmmy

**Category 1 — Basic Aggregations (9 measures):**
- Total Sales — SUM, $ format
- Total Profit — SUM, $ format
- Total Orders — DISTINCTCOUNT, whole number
- Total Quantity — SUM, whole number
- Profit Margin % — DIVIDE, % format
- Average Order Value — DIVIDE, $ format
- West Sales — CALCULATE + filter, $ format
- Sales % of Total — DIVIDE + CALCULATE + ALL, % format
- Profitable Orders — CALCULATE + condition, whole number

**Category 2 — Time Intelligence (9 measures):**
- Sales MTD — TOTALMTD, $ format
- Sales QTD — TOTALQTD, $ format
- Sales YTD — TOTALYTD, $ format
- Sales PY — CALCULATE + SAMEPERIODLASTYEAR, $ format
- Sales PM — CALCULATE + DATEADD(-1,MONTH), $ format
- Sales PQ — CALCULATE + DATEADD(-1,QUARTER), $ format
- YoY Growth — subtraction, $ format
- YoY Growth % — DIVIDE, % format
- MoM Growth % — DIVIDE, % format

**Category 3 — Iterator Functions (2 measures):**
- Average Selling Price — SUMX + DIVIDE, $ format
- Avg Discount % — AVERAGEX, % format

**Category 4 — Ranking (3 measures):**
- Product Rank — RANKX + ALL + DENSE, whole number
- Customer Rank — RANKX + ALL + DENSE, whole number
- Is Top 10 Product — IF + Product Rank, text

**Category 5 — VAR/RETURN (2 measures):**
- Sales Performance Status — VAR/RETURN + SWITCH(TRUE()), text
- Profit Category — VAR/RETURN + SWITCH(TRUE()), text

**Category 6 — Advanced (2 measures):**
- Running Total Sales — CALCULATE + FILTER + ALL + MAX, $ format
- Dynamic Title — SELECTEDVALUE + concatenation, text

**Documentation created:**
- DAX_Measures_CheatSheet.html
- DAX_Measures_CheatSheet.md

---

### Phase 5 — Report Building

**Commit:** (included in phase5-6-7 commit)

**Changes:**

**JSON Theme applied:**
- Created: Anshul_RA1_Theme_v2.json
- Location: 01_Sales_Analytics/templates/
- Applied via View → Themes → Browse for themes
- v1 had invalid properties (border radius, padding, page section)
- v2 fixed with Power BI compatible properties only

**Page 1 — Executive Overview:**
- Year slicer (Dropdown style)
- Region slicer (Dropdown style)
- 5 KPI cards: Total Sales ($2.3M), Total Profit ($286K), Profit Margin % (12.47%), Total Orders (5,009), Average Order Value ($458.61)
- Line chart: Monthly Sales Trend (X=Month Name sorted by Month Number, Y=Total Sales, Legend=Year)
- Bar chart: Sales by Category
- Treemap: Sales by Region (Note: Map visual disabled — org tenant restriction, Treemap used as alternative)
- Title text box: "Sales Analytics Dashboard"

**Page 2 — Product Performance:**
- Year slicer (Dropdown)
- Category slicer (Dropdown)
- Bar chart: Top 10 Products (Top N filter applied)
- Donut chart: Sales by Category
- Matrix: Category × Sub-Category with conditional formatting
- Title: "Product Performance"

**Page 3 — Customer Insights:**
- Year slicer (Dropdown)
- Segment slicer (Dropdown)
- Table: Top 10 Customers (Top N filter, Customer Name/Segment/Sales/Profit/Rank)
- Donut: Sales by Segment (Consumer, Corporate, Home Office)
- Bar chart: Sales by Region (West highest, South lowest)
- Title: "Customer Insights"

**Page 4 — Time Analysis:**
- Year slicer (Dropdown)
- 5 KPI cards: Sales MTD, Sales QTD, Sales YTD, YoY Growth %, MoM Growth %
- Line chart: Current Year vs Previous Year (YoY comparison)
- Column chart: Month over Month Growth %
- Matrix: Year × Month Sales with conditional formatting
- Title: "Time Analysis"
- Note: Select Year 2017 for populated values (data ends 2017)

**Page 5 — Order Details (Drill-through):**
- Drill-through enabled on Category field
- Table: Order ID, Order Date, Product Name, Customer Name, Region, Sales, Profit
- Back button for navigation
- Title: "Order Details"

---

### Phase 6 — Row Level Security (RLS)

**Commit:** (included in phase5-6-7 commit)

**Changes:**

**4 RLS roles created:**
- West_Region: dim_Region[Region] = "West"
- East_Region: dim_Region[Region] = "East"
- Central_Region: dim_Region[Region] = "Central"
- South_Region: dim_Region[Region] = "South"

**All roles verified via:**
- Modeling tab → View as → each role tested
- Confirmed data isolation per region
- Confirmed RLS applies across all 5 pages
- Confirmed drill-through respects RLS

---

### Phase 7 — Documentation

**Commit:** `docs(phase7): complete documentation suite`

**Documents created:**
- SRS.md (Software Requirements Specification)
- LLD.md (Low Level Design)
- UAT_Test_Plan.md (75 test cases — all PASS)
- Data_Dictionary.md (all tables and measures)
- Change_Log.md (this document)

**Supporting documents (created earlier):**
- DAX_Measures_CheatSheet.html
- DAX_Measures_CheatSheet.md
- PowerBI_JSON_Theme_Guide.html
- PowerBI_JSON_Theme_Guide.md

---

## Known Issues Log

| Issue ID | Description | Impact | Resolution | Status |
|----------|-------------|--------|------------|--------|
| ISS-001 | Map visual blocked by tenant admin | Cannot show geographic map | Used Treemap instead | Resolved |
| ISS-002 | JSON theme v1 import error | Theme not applied | Removed invalid properties, created v2 | Resolved |
| ISS-003 | Slicer font color not from theme | Minor styling | Manual override applied | Resolved |
| ISS-004 | Time Intelligence blank without date filter | MTD/YTD show blank | Select Year 2017 in slicer | Documented |
| ISS-005 | Power BI Service unavailable | Cannot publish | Desktop-only workflow | Accepted |
| ISS-006 | Month Name alphabetical sort | Wrong month order in charts | Sort by Month Number column applied | Resolved |

---

## Future Enhancements (Backlog)

| Priority | Enhancement | Phase |
|----------|------------|-------|
| HIGH | Power BI Service deployment | v1.1 |
| HIGH | Scheduled data refresh | v1.1 |
| MEDIUM | What-if parameter for sales targets | v1.2 |
| MEDIUM | Mobile layout optimization | v1.2 |
| LOW | Bookmarks for saved views | v1.3 |
| LOW | Custom tooltips on charts | v1.3 |
| LOW | Decomposition tree visual | v1.3 |

---

*Anshul-RA1 | DATA_ANALYTICS_POWERBI | Project 1 — Sales Analytics*
*Change Log Version 1.0 | May 2026*
