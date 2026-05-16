# User Acceptance Testing (UAT) Test Plan
## Project 1 — Sales Analytics Dashboard
### DATA_ANALYTICS_POWERBI | Anshul-RA1

---

**Document Version:** 1.0  
**Created:** May 2026  
**Author:** Anshul Raghuvanshi  
**Status:** Production Ready  

---

## Table of Contents

1. Testing Overview
2. Test Environment
3. Data Validation Tests
4. DAX Measure Tests
5. Report Interaction Tests
6. RLS Security Tests
7. Theme and Design Tests
8. Performance Tests
9. Test Results Summary

---

## 1. Testing Overview

### 1.1 Purpose
This UAT Test Plan validates that the Sales Analytics Dashboard meets all business and functional requirements defined in the SRS. Each test case includes the test ID, description, steps, expected result, and actual result.

### 1.2 Test Categories

| Category | Test IDs | Count |
|----------|----------|-------|
| Data Validation | DV-001 to DV-010 | 10 |
| DAX Measures | DM-001 to DM-027 | 27 |
| Report Interactions | RI-001 to RI-015 | 15 |
| RLS Security | RS-001 to RS-008 | 8 |
| Theme and Design | TD-001 to TD-010 | 10 |
| Performance | PF-001 to PF-005 | 5 |
| **Total** | | **75** |

---

## 2. Test Environment

| Component | Detail |
|-----------|--------|
| Tool | Power BI Desktop April 2026 (v2.153.1206.0) |
| Dataset | Sample-Superstore.xlsx |
| OS | Windows 10/11 |
| Theme | Anshul_RA1_Theme_v2.json |
| Git Branch | main (after all PRs merged) |

---

## 3. Data Validation Tests

### DV-001 — Row Count Verification
**Description:** Verify fact_Sales has correct number of rows
**Steps:**
1. Open Power BI Desktop
2. Go to Data view
3. Select fact_Sales table
4. Check row count at bottom

**Expected:** 9,994 rows  
**Actual:** ✅ 9,994 rows  
**Status:** PASS

---

### DV-002 — dim_Customer Unique Count
**Description:** Verify 793 unique customers
**Steps:**
1. Data view → dim_Customer
2. Count rows

**Expected:** 793 rows  
**Actual:** ✅ 793 rows  
**Status:** PASS

---

### DV-003 — dim_Product Unique Count
**Description:** Verify 1,000 unique products
**Steps:**
1. Data view → dim_Product
2. Count rows

**Expected:** 1,000 rows  
**Actual:** ✅ 1,000 rows  
**Status:** PASS

---

### DV-004 — dim_Date Row Count
**Description:** Verify 1,826 days in date table
**Steps:**
1. Data view → dim_Date
2. Count rows

**Expected:** 1,826 rows (2014-01-01 to 2018-12-31)  
**Actual:** ✅ 1,826 rows  
**Status:** PASS

---

### DV-005 — Postal Code Data Type
**Description:** Verify Postal Code is Text type
**Steps:**
1. Power Query Editor
2. dim_Region table
3. Check Postal Code column type

**Expected:** Text type (not Whole Number)  
**Actual:** ✅ Text type  
**Status:** PASS

---

### DV-006 — Order Date Data Type
**Description:** Verify Order Date is Date type
**Steps:**
1. Power Query Editor
2. fact_Sales table
3. Check Order Date column type

**Expected:** Date type  
**Actual:** ✅ Date type  
**Status:** PASS

---

### DV-007 — RAW_DATA Disabled
**Description:** Verify RAW_DATA does not load to model
**Steps:**
1. Power Query Editor
2. RAW_DATA query
3. Check Enable Load setting

**Expected:** Load disabled (staging only)  
**Actual:** ✅ Load disabled  
**Status:** PASS

---

### DV-008 — Relationship Verification
**Description:** Verify all 4 relationships exist and are active
**Steps:**
1. Model view
2. Check all relationship lines
3. Click each to verify properties

**Expected:** 4 active, single-direction, 1:Many relationships  
**Actual:** ✅ All 4 relationships correct  
**Status:** PASS

---

### DV-009 — Month Name Sort
**Description:** Verify Month Name sorts chronologically
**Steps:**
1. Report view
2. Add Month Name to a chart X-axis
3. Check order

**Expected:** January → February → March → ... → December  
**Actual:** ✅ Correct chronological order  
**Status:** PASS

---

### DV-010 — dim_Date Marked as Date Table
**Description:** Verify dim_Date is marked as Date Table
**Steps:**
1. Model view or Data view
2. Right click dim_Date
3. Check Mark as Date Table option

**Expected:** Marked as Date Table with Date column  
**Actual:** ✅ Correctly marked  
**Status:** PASS

---

## 4. DAX Measure Tests

### DM-001 — Total Sales
**Expected Value:** $2,297,200.86 (no filters)  
**Formula:** SUM(fact_Sales[Sales])  
**Actual:** ✅ $2.3M on card visual  
**Status:** PASS

---

### DM-002 — Total Profit
**Expected Value:** $286,397.02 (no filters)  
**Formula:** SUM(fact_Sales[Profit])  
**Actual:** ✅ $286K on card visual  
**Status:** PASS

---

### DM-003 — Total Orders
**Expected Value:** 5,009 unique orders  
**Formula:** DISTINCTCOUNT(fact_Sales[Order ID])  
**Actual:** ✅ 5,009 on card visual  
**Status:** PASS

---

### DM-004 — Total Quantity
**Expected Value:** 37,873 units  
**Formula:** SUM(fact_Sales[Quantity])  
**Actual:** ✅ 38K on card visual  
**Status:** PASS

---

### DM-005 — Profit Margin %
**Expected Value:** 12.47%  
**Formula:** DIVIDE([Total Profit],[Total Sales],0)  
**Actual:** ✅ 12.47% on card visual  
**Status:** PASS

---

### DM-006 — Average Order Value
**Expected Value:** $458.61  
**Formula:** DIVIDE([Total Sales],[Total Orders],0)  
**Actual:** ✅ $458.61 on card visual  
**Status:** PASS

---

### DM-007 — West Sales
**Test:** Apply Region slicer = East, West Sales should still show West value  
**Expected:** West Sales unchanged regardless of Region slicer  
**Actual:** ✅ West Sales constant at ~$725K  
**Status:** PASS

---

### DM-008 — Sales % of Total
**Test:** In table with Region breakdown, all % values should sum to 100%  
**Expected:** Sum of all region % = 100%  
**Actual:** ✅ Correct percentages summing to 100%  
**Status:** PASS

---

### DM-009 — Profitable Orders
**Expected:** Less than Total Orders (some orders have negative profit)  
**Actual:** ✅ 4,407 (less than 5,009 Total Orders)  
**Status:** PASS

---

### DM-010 — Sales MTD
**Test:** Select Year 2017, check December MTD  
**Expected:** December 2017 MTD = ~$83K  
**Actual:** ✅ $83K with Year 2017 selected  
**Status:** PASS

---

### DM-011 — Sales QTD
**Test:** Select Year 2017, check Q4 QTD  
**Expected:** Q4 2017 QTD = ~$280K  
**Actual:** ✅ $280K with Year 2017 selected  
**Status:** PASS

---

### DM-012 — Sales YTD
**Test:** Select Year 2017  
**Expected:** Full year 2017 total = ~$733K  
**Actual:** ✅ $733K with Year 2017 selected  
**Status:** PASS

---

### DM-013 — Sales PY
**Test:** 2017 selected → PY should return 2016 value  
**Expected:** 2016 total sales  
**Actual:** ✅ Returns 2016 sales value  
**Status:** PASS

---

### DM-014 — Sales PM
**Test:** November 2017 selected → PM = October 2017  
**Expected:** October 2017 sales  
**Actual:** ✅ Returns October 2017 value  
**Status:** PASS

---

### DM-015 — YoY Growth %
**Test:** Year 2017 selected  
**Expected:** ~20.36% growth vs 2016  
**Actual:** ✅ 20.36%  
**Status:** PASS

---

### DM-016 — MoM Growth %
**Test:** Year 2017 selected  
**Expected:** Negative small value for last month  
**Actual:** ✅ -0.02% MoM  
**Status:** PASS

---

### DM-017 — Average Selling Price
**Test:** Verify it differs from AVERAGE(Sales)  
**Expected:** Weighted average per unit (different from simple average)  
**Actual:** ✅ Shows weighted average correctly  
**Status:** PASS

---

### DM-018 — Avg Discount %
**Expected:** Value between 0% and 100%  
**Actual:** ✅ Shows reasonable discount percentage  
**Status:** PASS

---

### DM-019 — Product Rank
**Test:** Add to table with Product Name, verify rank 1 = highest sales  
**Expected:** Rank 1 = product with highest Total Sales  
**Actual:** ✅ DENSE ranking working correctly  
**Status:** PASS

---

### DM-020 — Customer Rank
**Test:** Add to table with Customer Name  
**Expected:** Rank 1 = customer with highest Total Sales  
**Actual:** ✅ Correct ranking  
**Status:** PASS

---

### DM-021 — Is Top 10 Product
**Test:** Filter table to "Top 10" — should show exactly 10 products  
**Expected:** Exactly 10 products when filtered to "Top 10"  
**Actual:** ✅ 10 products shown  
**Status:** PASS

---

### DM-022 — Sales Performance Status
**Test:** Year 2017 selected (20.36% growth)  
**Expected:** "Excellent (>10%)" since growth > 10%  
**Actual:** ✅ Shows correct status tier  
**Status:** PASS

---

### DM-023 — Profit Category
**Test:** No filter (12.47% margin)  
**Expected:** "Medium Margin (10-20%)" since 12.47% is between 10-20%  
**Actual:** ✅ "Medium Margin (10-20%)"  
**Status:** PASS

---

### DM-024 — Running Total Sales
**Test:** Add to monthly line chart — should be cumulative/always increasing  
**Expected:** Each month value >= previous month  
**Actual:** ✅ Cumulative running total  
**Status:** PASS

---

### DM-025 — Dynamic Title
**Test:** Select Year 2017 → title should update  
**Expected:** "Sales Analysis — 2017"  
**Actual:** ✅ Title updates dynamically  
**Status:** PASS

---

### DM-026 — YoY Growth (Amount)
**Test:** 2017 selected  
**Expected:** Positive dollar amount (since YoY% is positive 20.36%)  
**Actual:** ✅ Positive YoY growth amount  
**Status:** PASS

---

### DM-027 — Sales PQ
**Test:** Q4 2017 selected → PQ = Q3 2017  
**Expected:** Q3 2017 total sales  
**Actual:** ✅ Returns Q3 2017 value  
**Status:** PASS

---

## 5. Report Interaction Tests

### RI-001 — Year Slicer Filters All Visuals
**Steps:**
1. Page 1 → Select Year = 2016
2. Check all 5 KPI cards
3. Check line chart
4. Check bar chart

**Expected:** All visuals show 2016 data only  
**Actual:** ✅ All visuals filtered correctly  
**Status:** PASS

---

### RI-002 — Region Slicer Filters All Visuals
**Steps:**
1. Page 1 → Select Region = West
2. Check Total Sales card

**Expected:** Total Sales shows West region total only (~$725K)  
**Actual:** ✅ Correct filtering  
**Status:** PASS

---

### RI-003 — Multi-Slicer Interaction
**Steps:**
1. Select Year = 2017 AND Region = West
2. Check Total Sales

**Expected:** 2017 West sales only  
**Actual:** ✅ Both filters applied correctly  
**Status:** PASS

---

### RI-004 — Slicer Clear/Reset
**Steps:**
1. Select a year
2. Click eraser icon on slicer
3. Check values

**Expected:** Returns to All data (no filter)  
**Actual:** ✅ Correctly resets to All  
**Status:** PASS

---

### RI-005 — Line Chart Month Order
**Steps:**
1. Page 1 → Check X-axis of Monthly Sales Trend

**Expected:** January → February → ... → December  
**Actual:** ✅ Correct chronological order  
**Status:** PASS

---

### RI-006 — Bar Chart Category Order
**Steps:**
1. Page 1 → Check Sales by Category bar chart

**Expected:** Sorted by sales (Technology highest)  
**Actual:** ✅ Technology > Furniture > Office Supplies  
**Status:** PASS

---

### RI-007 — Top 10 Products Filter
**Steps:**
1. Page 2 → Check bar chart
2. Count bars

**Expected:** Exactly 10 product bars  
**Actual:** ✅ 10 products displayed  
**Status:** PASS

---

### RI-008 — Donut Chart Segments
**Steps:**
1. Page 3 → Check segment donut

**Expected:** 3 segments: Consumer, Corporate, Home Office  
**Actual:** ✅ All 3 segments visible  
**Status:** PASS

---

### RI-009 — Time Intelligence with Year Slicer
**Steps:**
1. Page 4 → Select Year 2017
2. Check all 5 KPI cards

**Expected:** MTD=$83K, QTD=$280K, YTD=$733K, YoY%=20.36%, MoM%=-0.02%  
**Actual:** ✅ All values correct  
**Status:** PASS

---

### RI-010 — Matrix Conditional Formatting
**Steps:**
1. Page 4 → Year × Month matrix
2. Check color coding

**Expected:** Higher values = darker/different color  
**Actual:** ✅ Conditional formatting applied  
**Status:** PASS

---

### RI-011 — Drill-through Navigation
**Steps:**
1. Page 2 → Right click on Technology bar
2. Select Drill through → Order Details

**Expected:** Navigates to Page 5 with Technology orders only  
**Actual:** ✅ Drill-through works correctly  
**Status:** PASS

---

### RI-012 — Back Button Navigation
**Steps:**
1. Navigate to Order Details via drill-through
2. Click Back button

**Expected:** Returns to previous page  
**Actual:** ✅ Back navigation works  
**Status:** PASS

---

### RI-013 — Cross Highlight
**Steps:**
1. Page 1 → Click on Technology in bar chart
2. Check line chart

**Expected:** Line chart highlights Technology data  
**Actual:** ✅ Cross-highlighting works  
**Status:** PASS

---

### RI-014 — Category Slicer Page 2
**Steps:**
1. Page 2 → Select Category = Technology
2. Check Top 10 products

**Expected:** Only Technology products shown  
**Actual:** ✅ Filtered correctly  
**Status:** PASS

---

### RI-015 — Segment Slicer Page 3
**Steps:**
1. Page 3 → Select Segment = Consumer
2. Check all visuals

**Expected:** Consumer data only  
**Actual:** ✅ Filtered correctly  
**Status:** PASS

---

## 6. RLS Security Tests

### RS-001 — West_Region Role Access
**Steps:**
1. Modeling tab → View as → West_Region
2. Check Total Sales on Page 1

**Expected:** Shows West region total only (not $2.3M)  
**Actual:** ✅ West total only displayed  
**Status:** PASS

---

### RS-002 — East_Region Role Access
**Steps:**
1. View as → East_Region
2. Check Total Sales

**Expected:** East region total only  
**Actual:** ✅ East total only displayed  
**Status:** PASS

---

### RS-003 — Central_Region Role Access
**Steps:**
1. View as → Central_Region
2. Check Total Sales

**Expected:** Central region total only  
**Actual:** ✅ Central total only displayed  
**Status:** PASS

---

### RS-004 — South_Region Role Access
**Steps:**
1. View as → South_Region
2. Check Total Sales

**Expected:** South region total only  
**Actual:** ✅ South total only displayed  
**Status:** PASS

---

### RS-005 — Role Isolation Verification
**Steps:**
1. View as → West_Region
2. Check if East data is visible anywhere

**Expected:** East data completely hidden  
**Actual:** ✅ No East data visible  
**Status:** PASS

---

### RS-006 — RLS on All Pages
**Steps:**
1. View as → West_Region
2. Navigate through all 5 pages

**Expected:** West data only on all pages  
**Actual:** ✅ RLS applied consistently across all pages  
**Status:** PASS

---

### RS-007 — RLS on Drill-through
**Steps:**
1. View as → West_Region
2. Drill through to Order Details

**Expected:** Only West region orders in detail table  
**Actual:** ✅ West orders only in Order Details  
**Status:** PASS

---

### RS-008 — Four Roles Created
**Steps:**
1. Modeling → Manage Roles
2. Verify all 4 roles exist

**Expected:** West_Region, East_Region, Central_Region, South_Region  
**Actual:** ✅ All 4 roles present with correct filters  
**Status:** PASS

---

## 7. Theme and Design Tests

### TD-001 — Theme Applied on All Pages
**Steps:**
1. Navigate through all 5 pages
2. Check background color

**Expected:** Dark navy #1E2130 background on all pages  
**Actual:** ✅ Consistent dark theme  
**Status:** PASS

---

### TD-002 — KPI Card Colors
**Steps:**
1. Page 1 → Check KPI card numbers

**Expected:** Blue (#4FACFE) numbers, grey labels  
**Actual:** ✅ Correct colors  
**Status:** PASS

---

### TD-003 — Chart Backgrounds
**Steps:**
1. Check all chart backgrounds

**Expected:** #252B3B dark background  
**Actual:** ✅ All charts dark  
**Status:** PASS

---

### TD-004 — Consistent Font
**Steps:**
1. Check fonts across all visuals

**Expected:** Segoe UI throughout  
**Actual:** ✅ Consistent font  
**Status:** PASS

---

### TD-005 — Visual Titles Present
**Steps:**
1. Check every visual has a title

**Expected:** All visuals have descriptive titles  
**Actual:** ✅ All titled  
**Status:** PASS

---

### TD-006 — Page Titles Present
**Steps:**
1. Check each page for title text box

**Expected:** Title on each of 5 pages  
**Actual:** ✅ All pages titled  
**Status:** PASS

---

### TD-007 — Slicer Style Dropdown
**Steps:**
1. Check all slicers are Dropdown style

**Expected:** Dropdown style (not List)  
**Actual:** ✅ All slicers dropdown  
**Status:** PASS

---

### TD-008 — dataColors Chart Series
**Steps:**
1. Check line chart — 4 years have 4 colors

**Expected:** Blue, Teal, Green, Gold for 2014-2017  
**Actual:** ✅ Correct colors from theme  
**Status:** PASS

---

### TD-009 — No Map Visual
**Steps:**
1. Confirm map replaced with Treemap

**Expected:** Treemap showing regions (map disabled)  
**Actual:** ✅ Treemap used correctly  
**Status:** PASS

---

### TD-010 — JSON Theme File Present
**Steps:**
1. Check 01_Sales_Analytics/templates/ folder

**Expected:** Anshul_RA1_Theme_v2.json present  
**Actual:** ✅ Theme file in correct location  
**Status:** PASS

---

## 8. Performance Tests

### PF-001 — Initial Load Time
**Expected:** Report loads within 10 seconds  
**Actual:** ✅ Loads within acceptable time  
**Status:** PASS

---

### PF-002 — Slicer Response Time
**Expected:** Visual updates within 3 seconds after slicer change  
**Actual:** ✅ Near-instant response  
**Status:** PASS

---

### PF-003 — Drill-through Response
**Expected:** Order Details page loads within 5 seconds  
**Actual:** ✅ Fast response  
**Status:** PASS

---

### PF-004 — All Pages Load
**Expected:** All 5 pages load without errors  
**Actual:** ✅ No errors on any page  
**Status:** PASS

---

### PF-005 — No Circular Dependencies
**Expected:** No circular dependency errors in DAX  
**Actual:** ✅ All 27 measures load correctly  
**Status:** PASS

---

## 9. Test Results Summary

| Category | Total Tests | Passed | Failed | Pass Rate |
|----------|------------|--------|--------|-----------|
| Data Validation | 10 | 10 | 0 | 100% |
| DAX Measures | 27 | 27 | 0 | 100% |
| Report Interactions | 15 | 15 | 0 | 100% |
| RLS Security | 8 | 8 | 0 | 100% |
| Theme and Design | 10 | 10 | 0 | 100% |
| Performance | 5 | 5 | 0 | 100% |
| **TOTAL** | **75** | **75** | **0** | **100%** |

---

## Known Issues / Limitations

| Issue | Impact | Workaround |
|-------|--------|------------|
| Map visual disabled (tenant restriction) | Cannot show geographic map | Treemap used instead |
| Time Intelligence shows BLANK without date filter | MTD/QTD/YTD blank by default | Add Year slicer and select a year |
| Power BI Service not available | Cannot publish or schedule refresh | Desktop-only access |
| Slicer values font color not from theme | Minor styling inconsistency | Manual font color override applied |

---

*Anshul-RA1 | DATA_ANALYTICS_POWERBI | Project 1 — Sales Analytics*
*UAT Test Plan Version 1.0 | May 2026*
*All 75 test cases: PASS ✅*
