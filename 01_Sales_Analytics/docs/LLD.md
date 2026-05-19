# Low Level Design (LLD)
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

1. Architecture Overview
2. Folder Structure
3. Git Workflow
4. Power Query Design
5. Data Model Design
6. DAX Architecture
7. Report Design
8. Theme Architecture
9. RLS Design
10. Performance Considerations

---

## 1. Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                   DATA SOURCES                       │
│          Sample-Superstore.xlsx (Excel)              │
└─────────────────────┬───────────────────────────────┘
                      │ Get Data → Excel
                      ▼
┌─────────────────────────────────────────────────────┐
│                  POWER QUERY (ETL)                   │
│  RAW_DATA → Reference Queries → 5 Clean Tables      │
│  dim_Customer, dim_Product, dim_Region,              │
│  dim_Date (M Language), fact_Sales                   │
└─────────────────────┬───────────────────────────────┘
                      │ Load to Model
                      ▼
┌─────────────────────────────────────────────────────┐
│               DATA MODEL (Star Schema)               │
│  dim_Date ──┐                                        │
│  dim_Customer──┤                                     │
│  dim_Product───┼──► fact_Sales (9,994 rows)          │
│  dim_Region ───┘                                     │
└─────────────────────┬───────────────────────────────┘
                      │ DAX Calculations
                      ▼
┌─────────────────────────────────────────────────────┐
│              DAX LAYER (_Measures table)             │
│  27 Measures: Basic → Time Intel → Iterator →        │
│  Ranking → VAR/RETURN → Advanced                     │
└─────────────────────┬───────────────────────────────┘
                      │ Report Visuals
                      ▼
┌─────────────────────────────────────────────────────┐
│              REPORT LAYER (5 Pages)                  │
│  Page 1: Executive Overview                          │
│  Page 2: Product Performance                         │
│  Page 3: Customer Insights                           │
│  Page 4: Time Analysis                               │
│  Page 5: Order Details (Drill-through)               │
└─────────────────────┬───────────────────────────────┘
                      │ Security
                      ▼
┌─────────────────────────────────────────────────────┐
│              SECURITY LAYER (RLS)                    │
│  West_Region | East_Region |                         │
│  Central_Region | South_Region                       │
└─────────────────────────────────────────────────────┘
```

---

## 2. Folder Structure

```
DATA_ANALYTICS_POWERBI/
│
├── 01_Sales_Analytics/
│   ├── data/
│   │   └── (empty — .gitignore excludes xlsx files)
│   ├── pbip/
│   │   └── Sales_Analytics_PBI.pbip
│   │   └── Sales_Analytics_PBI.SemanticModel/
│   │       └── definition/
│   │           ├── model.tmdl
│   │           └── tables/
│   │               ├── _Measures.tmdl
│   │               ├── dim_Customer.tmdl
│   │               ├── dim_Date.tmdl
│   │               ├── dim_Product.tmdl
│   │               ├── dim_Region.tmdl
│   │               └── fact_Sales.tmdl
│   │   └── Sales_Analytics_PBI.Report/
│   │       └── definition/
│   │           └── pages/
│   │               └── (page definition files)
│   ├── docs/
│   │   ├── SRS.md
│   │   ├── LLD.md
│   │   ├── UAT_Test_Plan.md
│   │   ├── Data_Dictionary.md
│   │   └── Change_Log.md
│   ├── dax/
│   │   └── (DAX measure reference files)
│   ├── templates/
│   │   └── Anshul_RA1_Theme_v2.json
│   └── screenshots/
│       ├── page1_executive_overview.png
│       ├── page2_product_performance.png
│       ├── page3_customer_insights.png
│       ├── page4_time_analysis.png
│       └── page5_order_details.png
│
├── 02_HR_Analytics/
│   └── (Project 2 — Pending)
│
├── 03_Financial_Reporting/
│   └── (Project 3 — Pending)
│
├── templates/
│   └── (shared templates)
│
├── .gitignore
├── LICENSE
└── README.md
```

---

## 3. Git Workflow

### 3.1 Branch Strategy

```
main (protected)
  └── develop
        ├── feature/phase1-data-connection
        ├── feature/phase2-3-star-schema
        ├── feature/phase4-dax-measures
        └── feature/phase5-6-7-report-rls-docs
```

### 3.2 Branch Protection Rules (main)
- Ruleset name: protect-main
- Require pull request before merging
- Direct push blocked (GH013 error expected)
- All changes via PR only

### 3.3 Commit Convention

```
Format: type(scope): description

Types:
feat     = new feature
fix      = bug fix
docs     = documentation
chore    = maintenance
refactor = code improvement

Examples:
feat(phase1): connect superstore dataset
feat(phase4): 27 DAX measures complete
docs(phase7): add SRS and LLD documents
chore: sync develop to main
```

### 3.4 PR Process
```
1. Create feature branch from develop
2. Make changes
3. git add .
4. git commit -m "conventional message"
5. git push origin feature/branch-name
6. Create PR: feature → develop
7. Merge PR
8. Create PR: develop → main
9. Merge PR
```

---

## 4. Power Query Design

### 4.1 Query Dependencies

```
RAW_DATA (load disabled — staging only)
    │
    ├── Reference → dim_Customer
    │   Columns kept: Customer ID, Customer Name, Segment
    │   Rows: 793 unique customers
    │
    ├── Reference → dim_Product
    │   Columns kept: Product ID, Category, Sub-Category, Product Name
    │   Rows: 1,000 unique products
    │
    ├── Reference → dim_Region
    │   Columns kept: Postal Code, City, State, Country, Region
    │   Rows: 631 unique locations
    │
    ├── Reference → fact_Sales
    │   Columns kept: Row ID, Order ID, Order Date, Ship Date,
    │                 Ship Mode, Customer ID, Product ID,
    │                 Postal Code, Sales, Quantity, Discount, Profit
    │   Rows: 9,994 transactions
    │
    └── Built from scratch → dim_Date (M Language)
        Date range: 2014-01-01 to 2018-12-31
        Rows: 1,826 calendar days
```

### 4.2 dim_Date M Language Code

```m
let
    StartDate = #date(2014, 1, 1),
    EndDate = #date(2018, 12, 31),
    DateList = List.Dates(StartDate, 
                          Duration.Days(EndDate - StartDate) + 1, 
                          #duration(1, 0, 0, 0)),
    DateTable = Table.FromList(DateList, 
                               Splitter.SplitByNothing(), 
                               {"Date"}),
    ChangedType = Table.TransformColumnTypes(DateTable, 
                                             {{"Date", type date}}),
    AddYear = Table.AddColumn(ChangedType, "Year", 
                              each Date.Year([Date]), Int64.Type),
    AddMonthNum = Table.AddColumn(AddYear, "Month Number", 
                                  each Date.Month([Date]), Int64.Type),
    AddMonthName = Table.AddColumn(AddMonthNum, "Month Name", 
                                   each Date.MonthName([Date]), type text),
    AddQuarter = Table.AddColumn(AddMonthName, "Quarter", 
                                 each "Q" & 
                                 Text.From(Date.QuarterOfYear([Date])), 
                                 type text),
    AddDayName = Table.AddColumn(AddQuarter, "Day of Week Name", 
                                 each Date.DayOfWeekName([Date]), type text),
    AddIsWeekend = Table.AddColumn(AddDayName, "Is Weekend", 
                                   each Date.DayOfWeek([Date]) >= 5, 
                                   type logical)
in
    AddIsWeekend
```

### 4.3 Data Type Standards

| Column | Correct Type | Reason |
|--------|-------------|--------|
| Order Date | Date | Time Intelligence requires date type |
| Ship Date | Date | Date calculations |
| Postal Code | Text | Prevents leading zero loss |
| Sales | Decimal Number | Currency precision |
| Profit | Decimal Number | Can be negative |
| Discount | Decimal Number | Percentage 0-1 |
| Quantity | Whole Number | Integer units |

---

## 5. Data Model Design

### 5.1 Star Schema Diagram

```
          dim_Date
          [Date PK]
              │ 1
              │
              │ *
    dim_Customer ──────── fact_Sales ──────── dim_Product
    [Customer ID PK]    [Row ID PK]         [Product ID PK]
                        [Order ID]
                        [Order Date FK]→dim_Date
                        [Customer ID FK]→dim_Customer
                        [Product ID FK]→dim_Product
                        [Postal Code FK]→dim_Region
                        [Sales]
                        [Quantity]
                        [Discount]
                        [Profit]
                              │ *
                              │
                              │ 1
                         dim_Region
                         [Postal Code PK]
```

### 5.2 Table Definitions

**fact_Sales**
| Column | Type | Role |
|--------|------|------|
| Row ID | Whole Number | Primary Key |
| Order ID | Text | Degenerate dimension |
| Order Date | Date | FK → dim_Date |
| Ship Date | Date | Date reference |
| Ship Mode | Text | Attribute |
| Customer ID | Text | FK → dim_Customer (hidden) |
| Product ID | Text | FK → dim_Product (hidden) |
| Postal Code | Text | FK → dim_Region (hidden) |
| Sales | Decimal | Measure source |
| Quantity | Whole Number | Measure source |
| Discount | Decimal | Measure source |
| Profit | Decimal | Measure source |

**dim_Customer**
| Column | Type | Role |
|--------|------|------|
| Customer ID | Text | Primary Key |
| Customer Name | Text | Display |
| Segment | Text | Category (Consumer/Corporate/Home Office) |

**dim_Product**
| Column | Type | Role |
|--------|------|------|
| Product ID | Text | Primary Key |
| Category | Text | Level 1 hierarchy |
| Sub-Category | Text | Level 2 hierarchy |
| Product Name | Text | Display |

**dim_Region**
| Column | Type | Role |
|--------|------|------|
| Postal Code | Text | Primary Key |
| City | Text | Display |
| State | Text | Geography level |
| Country | Text | Geography level |
| Region | Text | RLS filter column |

**dim_Date**
| Column | Type | Role |
|--------|------|------|
| Date | Date | Primary Key (marked as Date Table) |
| Year | Whole Number | Filter/Group |
| Month Number | Whole Number | Sort column for Month Name |
| Month Name | Text | Display (sorted by Month Number) |
| Quarter | Text | Filter/Group |
| Day of Week Name | Text | Filter/Group |
| Is Weekend | Logical | Filter |

### 5.3 Relationship Settings
- All relationships: Active
- All cardinality: Many to One (fact to dimension)
- All cross-filter direction: Single
- No bidirectional relationships (performance best practice)

---

## 6. DAX Architecture

### 6.1 Measure Organization

```
_Measures table
├── Category 1 — Basic Aggregations (9)
│   Measures 01-09
│
├── Category 2 — Time Intelligence (9)
│   Measures 10-18
│   Prerequisite: dim_Date marked as Date Table
│
├── Category 3 — Iterator Functions (2)
│   Measures 19-20
│   Row by row calculations using SUMX, AVERAGEX
│
├── Category 4 — Ranking (3)
│   Measures 21-23
│   RANKX with ALL() pattern
│
├── Category 5 — VAR/RETURN (2)
│   Measures 24-25
│   Multi-condition SWITCH(TRUE()) pattern
│
└── Category 6 — Advanced (2)
    Measures 26-27
    Running total and dynamic title
```

### 6.2 Key DAX Patterns Used

**Pattern 1 — Safe Division**
```dax
DIVIDE(numerator, denominator, 0)
-- Never use / operator
-- Third parameter handles divide by zero
```

**Pattern 2 — Filter Context Modification**
```dax
CALCULATE(
    [Total Sales],
    ALL(fact_Sales)         -- Remove all filters
)
```

**Pattern 3 — Time Intelligence**
```dax
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR(dim_Date[Date])
)
-- Requires marked Date Table
```

**Pattern 4 — VAR/RETURN**
```dax
VAR _CurrentSales  = [Total Sales]
VAR _PreviousSales = [Sales PY]
VAR _Growth = DIVIDE(_CurrentSales - _PreviousSales, _PreviousSales, 0)
RETURN
    SWITCH(TRUE(),
        _Growth >= 0.1, "Excellent",
        _Growth >= 0,   "Growing",
        _Growth >= -0.1,"Declining",
                        "Critical"
    )
```

**Pattern 5 — RANKX with ALL()**
```dax
RANKX(
    ALL(dim_Product),    -- Consider all products
    [Total Sales],       -- Rank by this value
    ,                    -- Current row value (default)
    DESC,                -- Highest = Rank 1
    DENSE                -- No gaps in ranking
)
```

**Pattern 6 — Running Total**
```dax
CALCULATE(
    [Total Sales],
    FILTER(
        ALL(dim_Date),
        dim_Date[Date] <= MAX(dim_Date[Date])
    )
)
```

---

## 7. Report Design

### 7.1 Page Specifications

| Page | Name | Visuals | Slicers | Primary Insight |
|------|------|---------|---------|-----------------|
| 1 | Executive Overview | Line, Bar, Treemap, 5 Cards | Year, Region | Overall performance |
| 2 | Product Performance | Bar (Top 10), Donut, Matrix | Year, Category | Product analysis |
| 3 | Customer Insights | Table, Donut, Bar | Year, Segment | Customer analysis |
| 4 | Time Analysis | Line, Column, Matrix, 5 Cards | Year | Trend analysis |
| 5 | Order Details | Table | (Drill-through) | Transaction detail |

### 7.2 Visual Selection Rationale

| Visual | Used For | Reason |
|--------|----------|--------|
| Line Chart | Monthly trends | Best for time series |
| Bar Chart | Category comparison | Easy ranking comparison |
| Treemap | Regional sales | Shows proportion by area |
| Donut | Segment split | Part-to-whole relationship |
| Card | KPIs | Single metric prominence |
| Matrix | Year × Month | Cross-tabulation |
| Table | Detail data | Row-level information |

### 7.3 Interaction Design
- All slicers affect all visuals on same page
- Drill-through: Right click → Drill through → Order Details
- Back button on Order Details returns to source page
- Cross-highlighting enabled between visuals

---

## 8. Theme Architecture

### 8.1 JSON Theme File
- File: Anshul_RA1_Theme_v2.json
- Location: 01_Sales_Analytics/templates/
- Applied via: View tab → Themes → Browse for themes

### 8.2 Theme Hierarchy
```
Global wildcard "*" (lowest priority)
    → Sets Segoe UI font, 11pt, #252B3B background
    
Visual-specific overrides
    → card: labels 28pt #4FACFE, categoryLabels #A0AEC0
    → slicer: header white, items white
    → lineChart: axis labels #A0AEC0, gridlines #2D3748
    → matrix: alternating rows (zebra striping)
    
Manual overrides (highest priority)
    → Slicer values font color (Power BI limitation)
    → Page background color (#1E2130)
```

### 8.3 Known Limitations
- Slicer values font color not applied by theme (manual override required)
- Page background must be set manually per page
- Map visual disabled at tenant level (Treemap used instead)

---

## 9. RLS Design

### 9.1 Implementation

```
Table: dim_Region
Filter column: Region
Relationship chain: dim_Region → fact_Sales (1:Many)

When West_Region role active:
dim_Region filtered to Region = "West"
→ fact_Sales filtered to West orders only
→ All visuals show West data only
→ Automatic via relationship propagation ✅
```

### 9.2 Role Definitions

| Role | DAX Expression | Records Visible |
|------|---------------|-----------------|
| West_Region | [Region] = "West" | ~31.5% of data |
| East_Region | [Region] = "East" | ~29.4% of data |
| Central_Region | [Region] = "Central" | ~21.8% of data |
| South_Region | [Region] = "South" | ~17.0% of data |

### 9.3 Testing Method
```
Modeling tab → View as →
Select role → Verify:
- Total Sales changes to region total
- All visuals filtered to region
- Other regions not visible
```

---

## 10. Performance Considerations

| Practice | Implemented | Benefit |
|----------|-------------|---------|
| Measures over calculated columns | ✅ | Memory efficiency |
| VAR/RETURN pattern | ✅ | Single calculation, multiple references |
| Single direction relationships | ✅ | Faster query propagation |
| DIVIDE() over / operator | ✅ | Error prevention |
| RAW_DATA load disabled | ✅ | Reduced model size |
| FK columns hidden | ✅ | Cleaner user experience |
| Star schema (not flat table) | ✅ | Optimal query performance |
| Marked Date Table | ✅ | Enables Time Intelligence optimization |

---

*Anshul-RA1 | DATA_ANALYTICS_POWERBI | Project 1 — Sales Analytics*
*LLD Version 1.0 | May 2026*
