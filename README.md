# Factory-to-Customer Shipping Route Efficiency Analysis — Nassau Candy Distributor

**Type:** End-to-end data analytics portfolio project (Python → SQL → Power BI)
**Role simulated:** Data Analyst supporting logistics/operations decision-making
**Dataset:** 10,194 order-level shipment records | 18 fields | US & Canada | Jan 2024 – Dec 2025

---

## 1. Executive Summary & Business Problem

Nassau Candy Distributor ships candy products from a small network of factories to customers across the US and Canada. Leadership has rich order and shipment data, but no **route-level efficiency intelligence** — meaning decisions about carriers, regions, and shipping methods are currently made reactively rather than from evidence.

**Business questions this project answers:**
- Which factory → customer routes are consistently fast, and which are chronically slow?
- Which regions/states act as geographic bottlenecks (high volume + poor lead time)?
- How does shipping method (Standard, Second Class, First Class, Same Day) trade off speed vs. implied cost?
- Where should Nassau Candy prioritize logistics investment first?

**My approach:** clean and validate the raw order data in Python, engineer route- and lead-time-level features, answer structured business questions in SQL, and surface the findings in an interactive Power BI dashboard that non-technical stakeholders can filter by date, region, state, and ship mode.

---

## 2. Dataset Description

| Field | Type | Description |
|---|---|---|
| Row ID | Integer | Unique row identifier |
| Order ID | String | Unique order identifier (composite key incl. product) |
| Order Date | Date (DD-MM-YYYY) | Date the order was placed |
| Ship Date | Date (DD-MM-YYYY) | Date the order shipped |
| Ship Mode | Categorical | Standard Class / Second Class / First Class / Same Day |
| Customer ID | Integer | Unique customer identifier |
| Country/Region | Categorical | United States / Canada |
| City | String | Customer city |
| State/Province | String | Customer state or province (59 unique values) |
| Postal Code | String | Customer postal/ZIP code |
| Division | Categorical | Product division: Chocolate / Sugar / Other |
| Region | Categorical | Sales region: Pacific, Atlantic, Interior, Gulf |
| Product ID | String | Unique product identifier |
| Product Name | String | Product long name (e.g. "Wonka Bar - Milk Chocolate") |
| Sales | Float | Total sales value of the order line |
| Units | Integer | Units ordered |
| Gross Profit | Float | Sales − Cost |
| Cost | Float | Cost to manufacture |

**Supplementary reference tables (used for the route/geography layer):**
- **Factory coordinates** — 5 factories (Lot's O' Nuts, Wicked Choccy's, Sugar Shack, Secret Factory, The Other Factory) with latitude/longitude, used to plot factory → customer routes.
- **Product-to-factory mapping** — every `Product ID` is assigned to exactly one factory, which is what allows an order-level dataset to become a **route**-level dataset (Factory → Customer State/Region).

**Known data quality issue (documented, not hidden):** `Ship Date` values in the raw file are inconsistent with `Order Date` (some ship dates land years after the order date), producing implausible lead times. This is treated explicitly in the cleaning step below rather than ignored — a realistic scenario interviewers respond well to.

---

## 3. Key Insights & Business Recommendations

> Percentages below are placeholders — regenerate them from your own `queries.sql` output once the cleaned data is loaded, then replace the `XX%` values before publishing.

- **Route concentration risk:** The top 3 factory → region routes account for **~XX%** of total shipment volume, meaning any disruption at those factories has outsized delivery impact — recommend a documented backup carrier plan for these routes specifically.
- **Ship mode cost-speed tradeoff:** Same Day and First Class shipments cost proportionally more per unit but only reduce average lead time by **~XX%** versus Standard Class on certain routes — recommend restricting premium modes to orders above a minimum order value threshold.
- **Regional bottleneck:** The **[Region/State]** corridor shows both above-average lead time *and* above-average shipment volume — recommend prioritizing this corridor for carrier renegotiation or a regional fulfillment hub.
- **Chocolate division dependency:** Chocolate products make up **~96.5%** of shipment volume (9,844 of 10,194 rows) but ship from only 2 factories — recommend evaluating whether Sugar/Other division products (currently <4% of volume) justify their own dedicated shipping lanes or should be consolidated onto Chocolate routes.

---

## 4. Project Architecture & Setup Guide

```
nassau-candy-shipping-analysis/
├── README.md                     # This file
├── data/
│   ├── Nassau_Candy_Distributor.csv        # Raw source data
│   └── Nassau_Candy_Distributor_clean.csv  # Output of data_cleaning.py
├── data_cleaning.py               # Python cleaning & feature engineering script
├── queries.sql                    # SQL analysis (5 structured queries)
└── powerbi/
    └── Shipping_Route_Dashboard.pbix   # Power BI dashboard (build using powerbi_dashboard_spec.md)
```

### How to run

**1. Python cleaning step**
```bash
pip install pandas
python data_cleaning.py
```
This reads `data/Nassau_Candy_Distributor.csv`, validates and standardizes it, engineers `Shipping_Lead_Time_Days` and `Route` fields, and writes `data/Nassau_Candy_Distributor_clean.csv`.

**2. SQL analysis step**
- Load `Nassau_Candy_Distributor_clean.csv` into MySQL/PostgreSQL/SQLite (or any tool that supports standard SQL) as a table named `shipments`.
- Run the queries in `queries.sql` in order — each is commented with the business question it answers.

**3. Power BI step**
- Open Power BI Desktop → Get Data → import `Nassau_Candy_Distributor_clean.csv`.
- Build the dashboard using the layout, DAX measures, and filters specified in `powerbi_dashboard_spec.md`.

---

## 5. Tech Stack

`Python (pandas)` · `SQL (ANSI / MySQL-compatible)` · `Power BI (DAX)`

---


