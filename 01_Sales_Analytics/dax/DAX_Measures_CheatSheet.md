# DAX Measures Cheat Sheet
## Anshul-RA1 | DATA_ANALYTICS_POWERBI | Project 1 — Sales Analytics

> 27 Production-Grade Measures · 6 Categories · Interview Ready

---

## Quick Reference Table

| # | Measure Name | Category | Function(s) | Format | Difficulty |
|---|-------------|----------|-------------|--------|------------|
| 01 | Total Sales | Basic | SUM() | $ Currency | Beginner |
| 02 | Total Profit | Basic | SUM() | $ Currency | Beginner |
| 03 | Total Orders | Basic | DISTINCTCOUNT() | Whole Number | Beginner |
| 04 | Total Quantity | Basic | SUM() | Whole Number | Beginner |
| 05 | Profit Margin % | Basic | DIVIDE() | Percentage | Beginner |
| 06 | Average Order Value | Basic | DIVIDE() | $ Currency | Intermediate |
| 07 | West Sales | Basic | CALCULATE() | $ Currency | Intermediate |
| 08 | Sales % of Total | Basic | CALCULATE() ALL() DIVIDE() | Percentage | Intermediate |
| 09 | Profitable Orders | Basic | CALCULATE() | Whole Number | Intermediate |
| 10 | Sales MTD | Time Intelligence | TOTALMTD() | $ Currency | Intermediate |
| 11 | Sales QTD | Time Intelligence | TOTALQTD() | $ Currency | Intermediate |
| 12 | Sales YTD | Time Intelligence | TOTALYTD() | $ Currency | Intermediate |
| 13 | Sales PY | Time Intelligence | SAMEPERIODLASTYEAR() | $ Currency | Intermediate |
| 14 | Sales PM | Time Intelligence | DATEADD() | $ Currency | Intermediate |
| 15 | Sales PQ | Time Intelligence | DATEADD() | $ Currency | Intermediate |
| 16 | YoY Growth | Time Intelligence | Subtraction | $ Currency | Intermediate |
| 17 | YoY Growth % | Time Intelligence | DIVIDE() | Percentage | Intermediate |
| 18 | MoM Growth % | Time Intelligence | DIVIDE() | Percentage | Intermediate |
| 19 | Average Selling Price | Iterators | SUMX() DIVIDE() | $ Currency | Advanced |
| 20 | Avg Discount % | Iterators | AVERAGEX() | Percentage | Intermediate |
| 21 | Product Rank | Ranking | RANKX() ALL() | Whole Number | Advanced |
| 22 | Customer Rank | Ranking | RANKX() ALL() | Whole Number | Advanced |
| 23 | Is Top 10 Product | Ranking | IF() | Text | Intermediate |
| 24 | Sales Performance Status | VAR/RETURN | VAR SWITCH DIVIDE | Text | Advanced |
| 25 | Profit Category | VAR/RETURN | VAR SWITCH | Text | Advanced |
| 26 | Running Total Sales | Advanced | CALCULATE FILTER ALL MAX | $ Currency | Advanced |
| 27 | Dynamic Title | Advanced | SELECTEDVALUE() & | Text | Intermediate |

---

## Abbreviations Reference

| Abbreviation | Full Form | Meaning |
|-------------|-----------|---------|
| MTD | Month to Date | Sales from 1st of month to current date |
| QTD | Quarter to Date | Sales from 1st of quarter to current date |
| YTD | Year to Date | Sales from Jan 1st to current date |
| YoY | Year over Year | Comparing current period vs same period last year |
| MoM | Month over Month | Comparing current month vs previous month |
| QoQ | Quarter over Quarter | Comparing current quarter vs previous quarter |
| PY | Previous Year | Same period in the prior year |
| PM | Previous Month | The immediately preceding month |
| PQ | Previous Quarter | The immediately preceding quarter |
| KPI | Key Performance Indicator | A metric used to evaluate business performance |
| RLS | Row Level Security | Restricting data access based on user roles |
| FK | Foreign Key | Column linking to primary key in another table |
| PK | Primary Key | Unique identifier column in a dimension table |

---

## Category 1 — Basic Aggregations (9 Measures)

---

### Measure 01 — Total Sales

**Definition:** Calculates the grand total of all revenue. The foundation measure referenced by almost every other measure. Automatically respects all filter context from slicers and visual interactions.

**Formula:**
```dax
Total Sales = SUM(fact_Sales[Sales])
```

**Format:** Currency ($)

**Business Use:** Primary revenue KPI on every executive dashboard.

**Interview Q&A:**

**Q: Why SUM() and not SUMX() for Total Sales?**
> SUM() aggregates a single existing column in one operation — simpler and more performant. SUMX() is needed when a row-by-row calculation is required before aggregating (like multiplying two columns). Since Sales is already a pre-calculated revenue value in the fact table, SUM() is the correct and efficient choice.

**Q: How does Total Sales behave when a slicer is applied?**
> Total Sales automatically respects filter context. When Year = 2016 is selected, the measure evaluates only 2016 rows and returns the 2016 total. DAX measures evaluate within whatever filter context is active — they do not need explicit WHERE clauses like SQL.

---

### Measure 02 — Total Profit

**Definition:** Sums all profit values including negative values (losses). Negative profit indicates loss-making orders — typically caused by heavy discounting. Foundation for Profit Margin % calculation.

**Formula:**
```dax
Total Profit = SUM(fact_Sales[Profit])
```

**Format:** Currency ($)

**Business Use:** Profitability analysis. Negative Total Profit signals discounting problems or high-cost categories.

**Interview Q&A:**

**Q: What happens when Profit contains negative values?**
> SUM() handles negatives correctly — it algebraically adds all values. A negative Total Profit means the business made a loss in the selected context. Our dataset shows $286K total profit across ~$2.3M revenue — a 12.47% margin, which means the business is profitable but has room for improvement.

---

### Measure 03 — Total Orders

**Definition:** Counts unique orders using DISTINCTCOUNT. One Order ID appears multiple times in fact_Sales (once per line item), so COUNT would return wrong results. DISTINCTCOUNT ensures each order is counted exactly once.

**Formula:**
```dax
Total Orders = DISTINCTCOUNT(fact_Sales[Order ID])
```

**Format:** Whole Number

**Business Use:** Volume metric. Used in Average Order Value calculation.

**Interview Q&A:**

**Q: Why DISTINCTCOUNT instead of COUNT or COUNTROWS?**
> COUNT returns 9,994 (total rows) — wrong because the same Order ID repeats for multi-product orders. COUNTROWS gives the same wrong result. DISTINCTCOUNT returns 5,009 — the correct count of unique orders. The distinction matters because one order can contain dozens of line items.

**Q: What is the difference between COUNT, COUNTA, COUNTROWS, and DISTINCTCOUNT?**
> COUNT counts numeric non-blank values. COUNTA counts all non-blank values including text. COUNTROWS counts all rows in a table or filter result. DISTINCTCOUNT counts unique values only. For unique order/customer/product counts, DISTINCTCOUNT is always correct.

---

### Measure 04 — Total Quantity

**Definition:** Sums all units sold across all orders. Unlike Total Orders which counts transactions, Total Quantity measures actual product volume.

**Formula:**
```dax
Total Quantity = SUM(fact_Sales[Quantity])
```

**Format:** Whole Number

**Business Use:** Inventory and supply chain planning. Used in Average Selling Price calculation.

**Interview Q&A:**

**Q: What is the difference between Total Orders and Total Quantity?**
> Total Orders = 5,009 unique transactions. Total Quantity = 38,000+ total units sold. One order can contain 10 units of 5 different products — that is 1 order but 50 quantity. Both serve different purposes: orders measure transaction volume, quantity measures product volume.

---

### Measure 05 — Profit Margin %

**Definition:** Percentage of revenue that becomes profit. Uses DIVIDE() for safe division. Stored as decimal (0.1247) and displayed as 12.47% through Measure Tools formatting.

**Formula:**
```dax
Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)
```

**Format:** Percentage (%) — 2 decimal places

**Business Use:** Profitability health indicator. 12.47% margin = medium health.

**Interview Q&A:**

**Q: Why use DIVIDE() instead of the / operator?**
> The / operator throws a divide-by-zero error when Total Sales = 0, breaking the visual. DIVIDE() accepts a third parameter — the alternate result when the denominator is zero — returning 0 or BLANK instead of an error. Essential for production reports.

**Q: Why format as percentage in Measure Tools instead of multiplying by 100 in DAX?**
> Multiplying by 100 changes the underlying value from 0.1247 to 12.47, which breaks Time Intelligence and mathematical operations. The Format property only changes display — the stored decimal value remains mathematically correct for all downstream calculations.

---

### Measure 06 — Average Order Value

**Definition:** Average revenue per order = Total Sales / Total Orders. NOT the same as AVERAGE(Sales) which gives average per line item, ignoring order grouping.

**Formula:**
```dax
Average Order Value = DIVIDE([Total Sales], [Total Orders], 0)
```

**Format:** Currency ($)

**Business Use:** Customer spending behavior indicator. Rising AOV (Average Order Value) means customers buy more per transaction.

**Interview Q&A:**

**Q: Why not use AVERAGE(fact_Sales[Sales]) for Average Order Value?**
> AVERAGE(Sales) calculates the mean of individual line item values. One order with 3 items worth $100, $200, $300 would give AVERAGE = $200 — wrong! Our measure correctly treats the entire order as $600 divided by 1 order = $600 average order value. This is the true business metric.

---

### Measure 07 — West Sales

**Definition:** Demonstrates CALCULATE's ability to add a filter condition that overrides existing filter context. Always returns West region sales regardless of what region the user has selected in a slicer.

**Formula:**
```dax
West Sales = 
CALCULATE(
    [Total Sales],
    dim_Region[Region] = "West"
)
```

**Format:** Currency ($)

**Business Use:** Regional benchmarking. Compare any region against the fixed West baseline.

**Interview Q&A:**

**Q: What is CALCULATE and why is it the most important DAX function?**
> CALCULATE is the only DAX function that can modify filter context. Every other function passively respects existing filters. CALCULATE actively changes them — adding filters, removing them with ALL(), or replacing them entirely. Without CALCULATE, percentage of total, period comparisons, and conditional aggregations would be impossible.

---

### Measure 08 — Sales % of Total

**Definition:** Each row's contribution as a percentage of the absolute grand total. ALL(fact_Sales) removes every filter from the denominator so the grand total is always $2.3M regardless of active filters.

**Formula:**
```dax
Sales % of Total = 
DIVIDE(
    [Total Sales],
    CALCULATE([Total Sales], ALL(fact_Sales)),
    0
)
```

**Format:** Percentage (%) — 2 decimal places

**Business Use:** Contribution analysis by region, product, or customer.

**Interview Q&A:**

**Q: Why does this show 100% on a Card visual with no filters?**
> With no filters, numerator and denominator both = $2.3M, so $2.3M / $2.3M = 100%. This is mathematically correct. The measure is designed for table/matrix visuals where each row has its own filter context — there it correctly shows each row's % contribution.

**Q: What does ALL() do?**
> ALL() removes all filters from the specified table or column. Inside CALCULATE, ALL(fact_Sales) tells Power BI to ignore every filter — year, region, category — when calculating the denominator. This ensures the denominator always represents the true grand total.

---

### Measure 09 — Profitable Orders

**Definition:** Counts only orders where profit is greater than zero. Demonstrates CALCULATE filtering on a numeric condition rather than a dimension value.

**Formula:**
```dax
Profitable Orders = 
CALCULATE(
    [Total Orders],
    fact_Sales[Profit] > 0
)
```

**Format:** Whole Number

**Business Use:** If Profitable Orders is significantly less than Total Orders, the business has a serious discounting or pricing problem.

**Interview Q&A:**

**Q: Can CALCULATE filter on numeric conditions or only dimension values?**
> CALCULATE accepts any valid DAX Boolean expression — numeric conditions, date conditions, or complex expressions all work. fact_Sales[Profit] > 0 is internally converted to a FILTER() expression by Power BI during evaluation.

---

## Category 2 — Time Intelligence (9 Measures)

> **Prerequisite for ALL Time Intelligence functions:**
> 1. dim_Date table must exist with continuous dates, no gaps
> 2. dim_Date must be marked as Date Table in Power BI
> 3. dim_Date[Date] must be related to fact_Sales[Order Date]

---

### Measure 10 — Sales MTD (Month to Date)

**Definition:** Accumulates sales from the 1st day of the current month to the last date in context. Resets to zero at the start of every new month.

**Formula:**
```dax
Sales MTD = 
TOTALMTD(
    [Total Sales],
    dim_Date[Date]
)
```

**Format:** Currency ($)

**Business Use:** "How much have we sold this month so far?"

**Interview Q&A:**

**Q: What is the difference between MTD, QTD, and YTD?**
> MTD (Month to Date) accumulates from the 1st of the current month — resets monthly. QTD (Quarter to Date) accumulates from the 1st of the current quarter — resets every 3 months. YTD (Year to Date) accumulates from January 1st — resets annually. All three require a marked Date Table.

**Q: Why does MTD return BLANK with no date filter?**
> TOTALMTD calculates for the current system month (May 2026) which has no data in our 2014-2017 dataset. This is correct behavior — the measure works properly when a date slicer selects a period within the data range.

---

### Measure 11 — Sales QTD (Quarter to Date)

**Definition:** Accumulates sales from the 1st day of the current quarter to the last date in context. Quarters: Q1 (Jan-Mar), Q2 (Apr-Jun), Q3 (Jul-Sep), Q4 (Oct-Dec).

**Formula:**
```dax
Sales QTD = 
TOTALQTD(
    [Total Sales],
    dim_Date[Date]
)
```

**Format:** Currency ($)

**Business Use:** Quarterly business review tracking. Progress toward quarterly targets.

**Interview Q&A:**

**Q: How do you handle fiscal year quarters that don't start in January?**
> TOTALQTD accepts an optional third parameter — the fiscal year end date. TOTALQTD([Total Sales], dim_Date[Date], "03-31") sets fiscal year end to March 31, making quarters April-June, July-September, October-December, January-March.

---

### Measure 12 — Sales YTD (Year to Date)

**Definition:** Accumulates sales from January 1st of the current year to the last date in context. Most commonly used Time Intelligence measure in business reporting.

**Formula:**
```dax
Sales YTD = 
TOTALYTD(
    [Total Sales],
    dim_Date[Date]
)
```

**Format:** Currency ($)

**Business Use:** Annual target tracking. "Are we on pace to hit our annual revenue target?"

**Interview Q&A:**

**Q: What are the prerequisites for Time Intelligence functions in Power BI?**
> Three requirements: (1) A Date table with continuous dates covering the full data range with no gaps. (2) The table must be marked as a Date Table using the Mark as Date Table option. (3) The Date column must be related to the date column in the fact table. Without all three, Time Intelligence functions produce incorrect results.

---

### Measure 13 — Sales PY (Previous Year)

**Definition:** Returns sales for the same period in the previous year. SAMEPERIODLASTYEAR handles year shifting including leap year edge cases automatically.

**Formula:**
```dax
Sales PY = 
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR(dim_Date[Date])
)
```

**Format:** Currency ($)

**Business Use:** Year over Year (YoY) benchmarking baseline.

**Interview Q&A:**

**Q: Difference between SAMEPERIODLASTYEAR and DATEADD with -1 YEAR?**
> SAMEPERIODLASTYEAR is purpose-built for YoY comparison and handles leap year edge cases — February 29 in a leap year maps to February 28 in a non-leap year. DATEADD(-1, YEAR) is a general function that shifts any date by any number of periods. For annual comparisons, SAMEPERIODLASTYEAR is preferred for clarity and automatic edge case handling.

---

### Measure 14 — Sales PM (Previous Month)

**Definition:** Returns sales for the immediately preceding month using DATEADD with -1 MONTH offset. Foundation for MoM Growth % calculation.

**Formula:**
```dax
Sales PM = 
CALCULATE(
    [Total Sales],
    DATEADD(dim_Date[Date], -1, MONTH)
)
```

**Format:** Currency ($)

**Business Use:** "Did we improve from last month?"

**Interview Q&A:**

**Q: How flexible is DATEADD and what parameters does it accept?**
> DATEADD accepts three parameters: date column, number of intervals (positive or negative), and interval type (DAY, MONTH, QUARTER, or YEAR). Examples: DATEADD(Date, -3, MONTH) = 3 months ago, DATEADD(Date, 1, YEAR) = next year, DATEADD(Date, -7, DAY) = last week.

---

### Measure 15 — Sales PQ (Previous Quarter)

**Definition:** Returns sales for the immediately preceding quarter using DATEADD with -1 QUARTER offset.

**Formula:**
```dax
Sales PQ = 
CALCULATE(
    [Total Sales],
    DATEADD(dim_Date[Date], -1, QUARTER)
)
```

**Format:** Currency ($)

**Business Use:** Quarter over Quarter (QoQ) trend analysis.

**Interview Q&A:**

**Q: When would you use Sales PQ vs Sales PY?**
> Sales PY is for seasonal comparisons — Q4 2017 vs Q4 2016 accounts for seasonality. Sales PQ shows sequential trend — whether Q4 is better than Q3. A complete dashboard provides both perspectives.

---

### Measure 16 — YoY Growth (Year over Year Growth Amount)

**Definition:** Absolute dollar difference between current sales and same period last year. Positive = growth, Negative = decline.

**Formula:**
```dax
YoY Growth = [Total Sales] - [Sales PY]
```

**Format:** Currency ($)

**Business Use:** "We grew by $120,000 vs last year." Scale of growth in dollars.

**Interview Q&A:**

**Q: Why build YoY Growth as a separate measure instead of inline in YoY Growth %?**
> Separation of concerns. Building it standalone allows reuse in multiple places — card visuals, conditional formatting, VAR/RETURN patterns, and YoY Growth % formula. Each measure is simpler, independently testable, and maintainable.

---

### Measure 17 — YoY Growth % (Year over Year Growth Percentage)

**Definition:** Relative percentage change compared to same period last year. More comparable across different-sized periods and regions than absolute dollar growth.

**Formula:**
```dax
YoY Growth % = 
DIVIDE(
    [YoY Growth],
    [Sales PY],
    0
)
```

**Format:** Percentage (%) — 2 decimal places

**Business Use:** "We grew 20% vs last year." Comparable across regions of different sizes.

**Interview Q&A:**

**Q: What happens to YoY Growth % for the first year of data (2014)?**
> Sales PY returns BLANK for 2014 since there is no 2013 data. DIVIDE returns 0 (the third parameter alternate result). In production, this is communicated to users as "no prior year data available."

---

### Measure 18 — MoM Growth % (Month over Month Growth Percentage)

**Definition:** Percentage change between current month and previous month. Uses Sales PM as baseline.

**Formula:**
```dax
MoM Growth % = 
DIVIDE(
    [Total Sales] - [Sales PM],
    [Sales PM],
    0
)
```

**Format:** Percentage (%) — 2 decimal places

**Business Use:** Short-term performance monitoring. Tracks immediate impact of promotions or campaigns.

**Interview Q&A:**

**Q: When would you recommend MoM Growth % over YoY Growth % to stakeholders?**
> MoM is for short-term operational monitoring — immediate campaign impact, recent promotion results. YoY is better for strategic analysis because it accounts for seasonality — November 2017 vs November 2016 is more meaningful than November vs October which has natural seasonal differences. A complete dashboard provides both.

---

## Category 3 — Iterator Functions (2 Measures)

> **Iterator functions** end with X and work **row by row** before aggregating.
> Pattern: `FUNCTIONX(Table, Expression per row)`

---

### Measure 19 — Average Selling Price

**Definition:** True average revenue per unit sold. SUMX computes Sales × Quantity per row to get total revenue, then divides by total units. Fundamentally different from AVERAGE(Sales) which ignores quantity weighting.

**Formula:**
```dax
Average Selling Price = 
DIVIDE(
    SUMX(
        fact_Sales,
        fact_Sales[Sales] * fact_Sales[Quantity]
    ),
    SUM(fact_Sales[Quantity]),
    0
)
```

**Format:** Currency ($) — 2 decimal places

**Business Use:** Pricing analysis. Declining Average Selling Price signals pricing pressure or shift to cheaper products.

**Interview Q&A:**

**Q: Explain the difference between SUM and SUMX with a real example.**
> SUM aggregates an entire column in one operation. SUMX iterates row by row. Example: Row 1 has Sales=$100, Quantity=3. Row 2 has Sales=$200, Quantity=1. SUM(Sales) = $300 (just adds values). SUMX(fact_Sales, Sales * Quantity) = ($100×3) + ($200×1) = $500 (actual total revenue). SUMX is essential when the calculation requires combining multiple columns per row.

**Q: What is the naming pattern for iterator functions?**
> All iterator functions end with X: SUMX, AVERAGEX, COUNTX, MAXX, MINX, RANKX, PRODUCTX. They all share the same two-parameter structure — the table to iterate, and the expression to evaluate per row.

---

### Measure 20 — Avg Discount %

**Definition:** Average discount percentage across all orders using AVERAGEX. More extensible than AVERAGE() — can be applied to a filtered subset of rows for conditional discount analysis.

**Formula:**
```dax
Avg Discount % = 
AVERAGEX(
    fact_Sales,
    fact_Sales[Discount]
)
```

**Format:** Percentage (%) — 2 decimal places

**Business Use:** Discount management. High average discounts correlate with low profit margins.

**Interview Q&A:**

**Q: When would AVERAGEX give a different result from AVERAGE?**
> AVERAGEX becomes essential when the expression involves a row-level calculation — for example, AVERAGEX(fact_Sales, Sales / Quantity) calculates price per unit per row before averaging. Also critical when filtering: AVERAGEX(FILTER(fact_Sales, [Profit] > 0), [Discount]) gives average discount on profitable orders only — impossible with AVERAGE alone.

---

## Category 4 — Ranking (3 Measures)

---

### Measure 21 — Product Rank

**Definition:** Ranks each product by Total Sales using RANKX with DENSE ranking. ALL(dim_Product) ensures every product is considered in the ranking, not just the filtered subset.

**Formula:**
```dax
Product Rank = 
RANKX(
    ALL(dim_Product),
    [Total Sales],
    ,
    DESC,
    DENSE
)
```

**Format:** Whole Number

**Business Use:** Product performance league table. Identifies top sellers and underperformers.

**Interview Q&A:**

**Q: Explain all 5 parameters of RANKX.**
> Parameter 1: Table to rank across — ALL(dim_Product) includes all products. Parameter 2: Value to rank by — [Total Sales]. Parameter 3: Value for current row — blank = use default context value. Parameter 4: Order — DESC means highest sales = Rank 1, ASC means lowest = Rank 1. Parameter 5: Tie handling — DENSE means tied values share a rank with no gap (1,1,2,3), SKIP leaves a gap (1,1,3,4).

**Q: Why is ALL(dim_Product) necessary in RANKX?**
> Without ALL(), RANKX only considers products visible in the current filter context. If a visual shows only Furniture products, every Furniture product ranks against each other. With ALL(dim_Product), every product in the entire dataset is considered — Rank 1 is truly the best-selling product across all categories.

---

### Measure 22 — Customer Rank

**Definition:** Ranks each customer by Total Sales using the same RANKX pattern applied to dim_Customer. Identifies most valuable customers by revenue.

**Formula:**
```dax
Customer Rank = 
RANKX(
    ALL(dim_Customer),
    [Total Sales],
    ,
    DESC,
    DENSE
)
```

**Format:** Whole Number

**Business Use:** Customer value ranking. VIP identification, loyalty program targeting, account prioritization.

**Interview Q&A:**

**Q: How would you modify this to rank by profit instead of sales?**
> Replace [Total Sales] with [Total Profit] in the second parameter. This ranks customers by profitability — more valuable for decisions because a high-revenue customer with heavy discounts may actually be less profitable than a smaller customer paying full price.

---

### Measure 23 — Is Top 10 Product

**Definition:** Classifies each product as "Top 10" or "Others" based on Product Rank. Creates a text flag for visual filtering, conditional formatting, and color coding. Demonstrates measure composition.

**Formula:**
```dax
Is Top 10 Product = 
IF(
    [Product Rank] <= 10,
    "Top 10",
    "Others"
)
```

**Format:** Text

**Business Use:** Dynamic Top 10 filtering. Classification updates automatically when any filter changes.

**Interview Q&A:**

**Q: How is this measure used in a real report?**
> Three ways: (1) Visual filter — add to Filters pane, set to "Top 10" to show only top performers. (2) Conditional formatting — color Top 10 rows green, Others grey. (3) Grouping analysis — use as legend in a chart to show Top 10 vs Others sales contribution. The key: classification updates automatically with any slicer change.

---

## Category 5 — VAR / RETURN Pattern (2 Measures)

> **VAR** stores intermediate results. **RETURN** produces the final output.
> Benefits: Performance (calculated once), Readability, Debuggability.

---

### Measure 24 — Sales Performance Status

**Definition:** Stores intermediate calculations in named variables then uses SWITCH(TRUE()) to categorize Year over Year (YoY) Growth % into four performance tiers. Production-quality DAX demonstrating best practices.

**Formula:**
```dax
Sales Performance Status = 
VAR _CurrentSales  = [Total Sales]
VAR _PreviousSales = [Sales PY]
VAR _GrowthPct = 
    DIVIDE(
        _CurrentSales - _PreviousSales,
        _PreviousSales,
        0
    )
RETURN
    SWITCH(
        TRUE(),
        _GrowthPct >= 0.1,  "🟢 Excellent (>10%)",
        _GrowthPct >= 0,    "✅ Growing",
        _GrowthPct >= -0.1, "⚠️ Declining",
                            "🔴 Critical (>10% drop)"
    )
```

**Format:** Text

**Business Use:** At-a-glance performance indicator for executives. Status labels instead of raw numbers enable faster decisions.

**SWITCH Logic:**
```
_GrowthPct = 0.20 → 0.20 >= 0.1? YES → "🟢 Excellent"
_GrowthPct = 0.05 → 0.05 >= 0?   YES → "✅ Growing"
_GrowthPct = -0.05 → -0.05 >= -0.1? YES → "⚠️ Declining"
_GrowthPct = -0.20 → no match → "🔴 Critical"
```

**Interview Q&A:**

**Q: What are the advantages of VAR/RETURN over inline expressions?**
> Three advantages: (1) Performance — variables are calculated only once regardless of how many times referenced. Without VAR, [Sales PY] would calculate twice — once in numerator, once in denominator. (2) Readability — named variables make intent self-documenting. (3) Debuggability — change RETURN to return an intermediate variable (RETURN _GrowthPct) to inspect values during development.

**Q: How does SWITCH(TRUE()) work and why is it better than nested IF?**
> SWITCH(TRUE()) evaluates each condition in order and returns the first TRUE result, stopping immediately. Compared to nested IF, it is more readable, easier to maintain (add new tiers without restructuring), and more performant for multiple conditions.

---

### Measure 25 — Profit Category

**Definition:** Classifies current context into four profit margin categories using VAR to store Profit Margin % and SWITCH(TRUE()) for clean multi-condition logic.

**Formula:**
```dax
Profit Category = 
VAR _Margin = [Profit Margin %]
RETURN
    SWITCH(
        TRUE(),
        _Margin >= 0.2,  "High Margin (>20%)",
        _Margin >= 0.1,  "Medium Margin (10-20%)",
        _Margin >= 0,    "Low Margin (<10%)",
                         "Loss Making"
    )
```

**Format:** Text

**Business Use:** Margin health flagging. Quickly identify loss-making products, regions, or customer segments.

**Interview Q&A:**

**Q: Can VAR store a measure reference?**
> Yes — VAR can store the result of any DAX expression including measure references. VAR _Margin = [Profit Margin %] evaluates the measure once and stores the decimal result. Particularly useful when the same measure appears in multiple conditions — calculated once, reused many times = better performance.

---

## Category 6 — Advanced Measures (2 Measures)

---

### Measure 26 — Running Total Sales

**Definition:** Cumulative sum that keeps growing from the first date in the dataset with no annual reset. Unlike YTD (Year to Date) which resets every January 1st, Running Total accumulates from the beginning of all data continuously.

**Formula:**
```dax
Running Total Sales = 
CALCULATE(
    [Total Sales],
    FILTER(
        ALL(dim_Date),
        dim_Date[Date] <= MAX(dim_Date[Date])
    )
)
```

**Format:** Currency ($)

**Business Use:** Cumulative growth visualization. Revenue milestones. Shows absolute business built over time.

**Step-by-step Logic:**
```
Step 1: ALL(dim_Date) → remove all date filters
Step 2: FILTER → keep only dates ≤ MAX(dim_Date[Date])
Step 3: MAX(dim_Date[Date]) → last date in current row context
        Example: If visual row = February 2016 → MAX = Feb 29, 2016
Step 4: Filter keeps Jan 2014 → Feb 29, 2016
Step 5: CALCULATE evaluates [Total Sales] in this range
        Result = cumulative sales from day 1 through Feb 2016
```

**Interview Q&A:**

**Q: What is the difference between Running Total and YTD?**
> YTD resets to zero every January 1st — on Jan 1, 2016 it starts counting from zero again. Running Total never resets — it accumulates from the very first date in the dataset continuously. YTD tracks annual targets. Running Total shows absolute business growth over the entire company history.

**Q: Explain how MAX(dim_Date[Date]) works here.**
> MAX(dim_Date[Date]) returns the latest date in the current filter context. In a monthly table visual, when the row is February 2016, the context is February 2016 — so MAX returns February 29, 2016. This dynamically anchors the FILTER to the current row's date, making the cumulative calculation work correctly row by row.

---

### Measure 27 — Dynamic Title

**Definition:** Context-aware text string that changes based on the Year slicer selection. SELECTEDVALUE returns the selected value when exactly one item is chosen, and falls back to default text otherwise. The & operator concatenates strings.

**Formula:**
```dax
Dynamic Title = 
"Sales Analysis — " & 
SELECTEDVALUE(
    dim_Date[Year],
    "All Years"
)
```

**Format:** Text

**Business Use:** Professional report UX. Title automatically reads "Sales Analysis — 2016" when 2016 is selected.

**SELECTEDVALUE Behavior:**
```
User selects Year = 2016  → "Sales Analysis — 2016"
User selects nothing      → "Sales Analysis — All Years"
User selects 2016 + 2017  → "Sales Analysis — All Years"
```

**Interview Q&A:**

**Q: How does SELECTEDVALUE work and what is the second parameter for?**
> SELECTEDVALUE returns a column's value when exactly one distinct value is in the filter context. The second parameter is the alternate result when zero or more than one value is selected. Without it, SELECTEDVALUE returns BLANK for multi-selections, producing "Sales Analysis — " with nothing after the dash.

**Q: How is this measure used in a visual?**
> In the visual's Format pane → Title → enable it → click fx (conditional formatting) button in the Title text field → select the Dynamic Title measure. The title updates automatically whenever the slicer changes — no manual intervention required.

**Q: How would you extend this to show both Year and Region?**
> Extend the concatenation:
> ```dax
> "Sales Analysis — " & 
> SELECTEDVALUE(dim_Date[Year], "All Years") & 
> " | " & 
> SELECTEDVALUE(dim_Region[Region], "All Regions")
> ```
> Produces "Sales Analysis — 2016 | West" when both slicers have single selections.

---

## Key DAX Concepts for Interviews

### Filter Context vs Row Context

```
Filter Context:
→ The set of filters currently active from slicers, visuals, and relationships
→ Applied to EVERY measure automatically
→ Changes when user interacts with report

Row Context:
→ The current row being evaluated
→ Only exists inside calculated columns and iterator functions (SUMX, AVERAGEX etc.)
→ Does NOT exist in regular measures

Context Transition:
→ When CALCULATE is used inside an iterator
→ The row context is converted to an equivalent filter context
→ Advanced concept — important for senior interviews
```

### CALCULATE — The Golden Rule

```
CALCULATE(Expression, Filter1, Filter2, ...)

1. Takes existing filter context
2. Modifies it based on filter arguments
3. Evaluates Expression in the modified context
4. Returns the result

ALL() = Remove filters
ALLEXCEPT() = Remove all filters except specified
Direct filter = Add or replace filter
```

### SUM vs SUMX Decision Guide

```
Use SUM when:
→ Aggregating a single column directly
→ No row-level calculation needed
→ Example: SUM(fact_Sales[Sales])

Use SUMX when:
→ Multiplying or combining columns per row
→ Applying conditional logic per row
→ Iterating over a filtered subset
→ Example: SUMX(fact_Sales, Sales * Quantity)
```

### DIVIDE Best Practices

```
Always use DIVIDE() instead of /
Third parameter options:
→ DIVIDE(x, y, 0)        = Return 0 if error
→ DIVIDE(x, y, BLANK())  = Return blank if error
→ DIVIDE(x, y)           = Return blank by default

Never do: [Total Profit] / [Total Sales]
Always do: DIVIDE([Total Profit], [Total Sales], 0)
```

---

## Interview Preparation Checklist

```
✅ Can explain what Filter Context is
✅ Can explain why CALCULATE is the most important DAX function
✅ Can explain difference between SUM and SUMX with example
✅ Can explain why DISTINCTCOUNT and not COUNT for orders
✅ Can explain MTD, QTD, YTD full forms and difference
✅ Can explain YoY (Year over Year) calculation approach
✅ Can explain what ALL() does inside CALCULATE
✅ Can explain RANKX parameters (all 5)
✅ Can explain DENSE vs SKIP ranking
✅ Can explain VAR/RETURN benefits (3 reasons)
✅ Can explain SWITCH(TRUE()) vs nested IF
✅ Can explain SELECTEDVALUE and its second parameter
✅ Can explain Running Total vs YTD difference
✅ Can explain prerequisites for Time Intelligence functions
✅ Can explain why format percentage in Measure Tools not DAX
```

---

*Anshul-RA1 | DATA_ANALYTICS_POWERBI | Project 1 — Sales Analytics*
*27 DAX Measures | 6 Categories | Interview Ready | Production Grade*
