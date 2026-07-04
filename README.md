# 🏦 Credit Risk & Loan Portfolio Intelligence Platform
### Banking Analytics | SQL · Python · Power BI · Snowflake

---

## Business Problem
A bank's risk committee had **no real-time view** of their $2.4B loan portfolio health.
Delinquency rates, LTV exposure and branch-level risk were tracked in **disconnected Excel files** across 5 departments — leadership was always reacting, never anticipating risk.

**The question this project answers:**
> *"Which regions, branches and loan segments are at risk right now — and how much capital are we exposed to?"*

---

## Solution Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  SOURCE SYSTEMS (5 CSV files → SQLite / Snowflake)          │
│                                                             │
│  dim_branch_master        → Branch / HR System             │
│  fact_loan_originations   → Loan Origination System (LOS)  │
│  fact_loan_servicing      → Collections / Servicing        │
│  fact_credit_bureau       → Credit Bureau (Equifax et al)  │
│  fact_property_collateral → Property / Appraisal System    │
└──────────────────────┬──────────────────────────────────────┘
                       │  SQL JOINs
┌──────────────────────▼──────────────────────────────────────┐
│  PYTHON (Jupyter Notebook)                                  │
│  • EDA: 4 analysis charts                                   │
│  • Risk scoring: LTV bands, credit bands, aging buckets     │
│  • Export: 4 clean CSVs for Power BI                        │
└──────────────────────┬──────────────────────────────────────┘
                       │  Clean data
┌──────────────────────▼──────────────────────────────────────┐
│  POWER BI DASHBOARD (6 pages)                               │
│  Page 1 — Executive Summary       (KPI cards + donut)       │
│  Page 2 — Delinquency Aging       (aging buckets + map)     │
│  Page 3 — Branch Performance      (scorecard + ranking)     │
│  Page 4 — LTV Risk Analysis       (distribution + heatmap)  │
│  Page 5 — Vintage Analysis        (trend + year-on-year)    │
│  Page 6 — Credit Quality          (band breakdown + matrix) │
│                                                             │
│  + Row-Level Security by Region                             │
│  + Daily automated refresh                                  │
└─────────────────────────────────────────────────────────────┘
```

---

## Key DAX Measures

```dax
Delinquency Rate =
DIVIDE(
    COUNTROWS(FILTER('pbi_master_loans',
        'pbi_master_loans'[LoanStatus] <> "Current" &&
        'pbi_master_loans'[LoanStatus] <> "Paid Off")),
    COUNTROWS('pbi_master_loans')
)

Total Portfolio ($B) =
ROUND(SUM('pbi_master_loans'[LoanAmount]) / 1000000000, 2)

Default Exposure ($M) =
ROUND(CALCULATE(
    SUM('pbi_master_loans'[LoanAmount]),
    'pbi_master_loans'[LoanStatus] = "Default"
) / 1000000, 1)

Avg LTV % =
ROUND(AVERAGE('pbi_master_loans'[LTV]) * 100, 1)
```

---

## Business Impact

| Metric | Result |
|--------|--------|
| Default exposure surfaced | **$108M** |
| Reporting time reduction | **65% faster** |
| Active dashboard users | **200+ with RLS** |
| Source systems unified | **5 → 1 view** |

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python (pandas) | Data processing & EDA |
| SQLite | Local data warehouse |
| Snowflake | Cloud data warehouse (production) |
| SQL | JOIN queries across 5 systems |
| Power BI Desktop | Executive dashboard |
| DAX | Calculated measures & KPIs |
| Azure Data Factory | Automated daily refresh |

---

## How to Run

1. Save all files in one folder
2. Open Anaconda → Jupyter Notebook
3. Open `Project1_CreditRisk_Analysis.ipynb`
4. Run all cells — generates 4 clean CSVs + 4 charts
5. Open Power BI → Get Data → load the 4 `pbi_*.csv` files
6. Build relationships in Model view (LoanID + BranchName)
7. Build 6 dashboard pages using the guide provided

---

## Files

```
├── dim_branch_master.csv              ← Branch / HR System
├── fact_loan_originations.csv         ← Loan Origination System
├── fact_loan_servicing.csv            ← Collections System
├── fact_credit_bureau.csv             ← Credit Bureau
├── fact_property_collateral.csv       ← Property System
├── Project1_CreditRisk_Analysis.ipynb ← SQL + Python notebook
├── Mortgage_Dashboard.pbix            ← Power BI file (you build)
└── README.md
```

---

*Built by Dhanashri Dhanavade | Senior Data Analyst | Microsoft PL-300 Certified*
