# DATA_ANALYTICS_POWERBI

## Production-Grade Power BI Portfolio | Anshul Raghuvanshi

## About This Repository

This repository contains **3 production-grade Power BI dashboards** built for a Data Analyst job portfolio. Each project follows a complete **Software Development Lifecycle (SDLC)** — from requirements gathering to deployment — using professional tools and workflows.

**What makes this portfolio different:**

- ✅ Full SDLC documentation (SRS, LLD, UAT, Data Dictionary, Change Log)
- ✅ Professional Git workflow with branch protection and PR reviews
- ✅ Star Schema data modeling (not flat tables)
- ✅ Production-grade DAX measures with VAR/RETURN patterns
- ✅ Custom JSON theme design system
- ✅ Row Level Security (RLS) implementation
- ✅ PBIP format for Git-trackable Power BI files

---

## Tech Stack

| Tool                          | Usage                                    |
| ----------------------------- | ---------------------------------------- |
| Power BI Desktop (April 2026) | Report development                       |
| DAX                           | Data Analysis Expressions — all measures |
| Power Query (M Language)      | ETL and data transformation              |
| Git + GitHub                  | Version control and collaboration        |
| PBIP Format                   | Git-compatible Power BI project format   |
| JSON Theme                    | Custom report design system              |
| Star Schema                   | Data modeling pattern                    |

---

## Repository Structure

```
DATA_ANALYTICS_POWERBI/
│
├── 01_Sales_Analytics/          ✅ COMPLETE
│   ├── pbip/                    Power BI project files
│   ├── docs/                    SRS, LLD, UAT, Data Dictionary, Change Log
│   ├── templates/               JSON theme file
│   ├── screenshots/             Report page screenshots
│   └── dax/                     DAX measures reference
│
├── 02_HR_Analytics/             🔄 IN PROGRESS
│   ├── pbip/                    Power BI project files
│   ├── docs/                    Documentation (pending)
│   ├── templates/               JSON theme file
│   └── screenshots/             Report screenshots (pending)
│
├── 03_Financial_Reporting/      ⬜ PENDING
│   └── (Coming soon)
│
├── templates/                   Shared templates
├── .gitignore                   Excludes data files
├── LICENSE                      MIT License
└── README.md                    This file
```

---

## Projects Overview

---

## ✅ Project 1 — Sales Analytics Dashboard

**Status: PRODUCTION READY**

### Business Problem

A US-based retail company needed a comprehensive analytics solution to understand sales performance, profitability, customer behavior, and time-based trends across four regions.

### Dataset

| Attribute  | Detail                                 |
| ---------- | -------------------------------------- |
| Source     | Sample Superstore (Excel)              |
| Rows       | 9,994 order line items                 |
| Columns    | 21                                     |
| Date Range | January 2014 — December 2017           |
| Geography  | United States                          |
| Categories | Furniture, Office Supplies, Technology |

### Key Metrics Delivered

| KPI                 | Value         |
| ------------------- | ------------- |
| Total Revenue       | $2,297,200.86 |
| Total Profit        | $286,397.02   |
| Profit Margin       | 12.47%        |
| Total Orders        | 5,009         |
| Average Order Value | $458.61       |
| YoY Growth (2017)   | 20.36%        |

### Technical Architecture

**Data Model — Star Schema**

```
dim_Date ──────┐
dim_Customer ──┤
dim_Product ───┼──► fact_Sales (9,994 rows)
dim_Region ────┘
```

**DAX Measures — 27 Total**

| Category           | Measures                                             | Count |
| ------------------ | ---------------------------------------------------- | ----- |
| Basic Aggregations | Total Sales, Profit, Orders, Quantity, Margin %, AOV | 9     |
| Time Intelligence  | MTD, QTD, YTD, PY, PM, PQ, YoY%, MoM%                | 9     |
| Iterator Functions | Avg Selling Price (SUMX), Avg Discount (AVERAGEX)    | 2     |
| Ranking            | Product Rank, Customer Rank, Is Top 10 (RANKX)       | 3     |
| VAR/RETURN         | Sales Performance Status, Profit Category            | 2     |
| Advanced           | Running Total, Dynamic Title                         | 2     |

**Report Pages — 5 Total**

| Page                | Key Visuals                                     | Purpose                |
| ------------------- | ----------------------------------------------- | ---------------------- |
| Executive Overview  | 5 KPI cards, Line chart, Bar chart, Treemap     | High-level performance |
| Product Performance | Top 10 bar, Donut, Matrix                       | Product analysis       |
| Customer Insights   | Top 10 table, Donut, Bar chart                  | Customer analysis      |
| Time Analysis       | MTD/QTD/YTD cards, YoY line, MoM column, Matrix | Trend analysis         |
| Order Details       | Drill-through table, Back button                | Transaction detail     |

**Security — RLS (Row Level Security)**

| Role           | Filter             | Access            |
| -------------- | ------------------ | ----------------- |
| West_Region    | Region = "West"    | West data only    |
| East_Region    | Region = "East"    | East data only    |
| Central_Region | Region = "Central" | Central data only |
| South_Region   | Region = "South"   | South data only   |

### Documentation Suite

- 📄 [SRS.md](01_Sales_Analytics/docs/SRS.md) — Software Requirements Specification
- 📐 [LLD.md](01_Sales_Analytics/docs/LLD.md) — Low Level Design & Architecture
- ✅ [UAT_Test_Plan.md](01_Sales_Analytics/docs/UAT_Test_Plan.md) — 75 Test Cases (All PASS)
- 📚 [Data_Dictionary.md](01_Sales_Analytics/docs/Data_Dictionary.md) — All tables & measures
- 📝 [Change_Log.md](01_Sales_Analytics/docs/Change_Log.md) — Complete version history

### Git History

```
✅ Phase 0 — Setup & Environment
✅ Phase 1 — Data Connection
✅ Phase 2 — Power Query (5 tables)
✅ Phase 3 — Data Modeling (Star Schema)
✅ Phase 4 — 27 DAX Measures
✅ Phase 5 — 5 Page Report
✅ Phase 6 — RLS (4 roles)
✅ Phase 7 — Documentation
✅ Phase 8 — Final Git Commit
```

### Known Limitations

- Map visual disabled (tenant admin restriction — Treemap used instead)
- Time Intelligence shows BLANK without date filter (data ends 2017, system date 2026)
- Power BI Service deployment pending (personal account limitation)

---

## 🔄 Project 2 — HR Analytics Dashboard

**Status: IN PROGRESS — Phase 3 of 9**

### Business Problem

An organization needed to understand employee attrition patterns, identify at-risk employees, analyze salary distributions, and monitor satisfaction scores to improve retention strategies.

### Dataset

| Attribute   | Detail                                               |
| ----------- | ---------------------------------------------------- |
| Source      | IBM HR Analytics Employee Attrition Dataset (Kaggle) |
| Rows        | 1,470 employees                                      |
| Columns     | 35 attributes                                        |
| Key Metric  | Attrition (Yes/No)                                   |
| Departments | Sales, R&D, Human Resources                          |

### Target Deliverables

- 📊 6-page interactive report
- 🧮 25+ DAX measures (HR-specific metrics)
- 🤖 AI Visuals (Key Influencers, Smart Narratives)
- 🔒 Department-based RLS
- 📝 Complete documentation suite

### Planned DAX Measures

| Category      | Measures                                                             |
| ------------- | -------------------------------------------------------------------- |
| Headcount     | Total Employees, Active Employees, Total Attrition, Attrition Rate % |
| Salary        | Avg Monthly Income, Income by Dept, Total Salary Cost                |
| Tenure        | Avg Years at Company, Avg Years in Role, Tenure Band                 |
| Satisfaction  | Avg Job Satisfaction, Work Life Balance Score, Overall Score         |
| Performance   | High Performer Count, Performance Avg, Promotion Overdue             |
| Risk Analysis | Flight Risk Score, At Risk Employees, Department Risk Flag           |

### Planned Report Pages

| Page                       | Focus                                                 |
| -------------------------- | ----------------------------------------------------- |
| HR Overview                | Total employees, attrition KPIs, department breakdown |
| Attrition Analysis         | By department, age band, job role, overtime impact    |
| Salary Analysis            | By department, job level, gender pay analysis         |
| Employee Demographics      | Age distribution, education, marital status           |
| Satisfaction & Performance | Key Influencers AI visual, satisfaction heatmap       |
| Employee Detail            | Drill-through individual employee profile             |

### New Skills — Project 2 Introduces

- 🤖 **AI Visuals** — Key Influencers, Smart Narratives
- 👤 **PII Data Handling** — Employee privacy and data governance
- 🔐 **Sensitivity Labels** — Microsoft Information Protection
- 📚 **Bookmarks** — Custom navigation and storytelling
- 📅 **DATEDIFF Calculations** — Tenure and age calculations
- 🎯 **Advanced RLS** — Department-level security

### Progress

```
✅ Phase 0 — Setup (repo already exists)
✅ Phase 1 — Data Connection (CSV connected)
✅ Phase 2 — Power Query ETL (5 tables created)
🔄 Phase 3 — Data Modeling ← CURRENT
⬜ Phase 4 — DAX Measures (25+)
⬜ Phase 5 — Report Building (6 pages)
⬜ Phase 6 — AI Visuals
⬜ Phase 7 — RLS (Department roles)
⬜ Phase 8 — Documentation
⬜ Phase 9 — Final Git Commit
```

---

## ⬜ Project 3 — Financial Reporting Dashboard

**Status: PENDING — Starting after Project 2**

### Planned Features

- 📈 P&L (Profit & Loss) reporting structure
- 📅 Fiscal year DAX calculations
- 🎛️ What-If parameters for scenario modeling
- 🚀 Deployment pipelines (Dev → Test → Production)
- ⚡ Incremental refresh for large datasets
- 💰 Budget vs Actual variance analysis
- 🌊 Waterfall charts for financial storytelling

### New Skills — Project 3 Will Introduce

- Fiscal year Time Intelligence
- What-If parameters
- Power BI Service deployment
- Incremental refresh
- P&L financial structure in DAX
- Multi-source data integration

---

## Git Workflow

```
main (protected — PR required)
  └── develop
        ├── feature/phase0-setup
        ├── feature/phase1-data-connection
        ├── feature/phase2-3-star-schema
        ├── feature/phase4-dax-measures
        ├── feature/phase5-6-7-report-rls-docs
        └── feature/hr-phase1-2-data-powerquery
```

### Branch Protection Rules

- Direct push to main: ❌ BLOCKED
- Pull Request required: ✅
- Ruleset name: protect-main

### Commit Convention

```
feat(scope):     New feature
fix(scope):      Bug fix
docs(scope):     Documentation
chore(scope):    Maintenance
refactor(scope): Code improvement
```

---

## Author

**Anshul Raghuvanshi**

- GitHub: [@Anshul-RA1](https://github.com/Anshul-RA1)
- Repository: [DATA_ANALYTICS_POWERBI](https://github.com/Anshul-RA1/DATA_ANALYTICS_POWERBI)

_Last Updated: May 2026_
_Project 1: Complete ✅ | Project 2: In Progress 🔄 | Project 3: Pending ⬜_
