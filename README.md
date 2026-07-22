# 📊 Procurement Category Analysis — Power BI Dashboard

An end-to-end procurement analytics solution built for **Procmart Private Limited**, designed to give leadership a real-time, drill-down view of revenue, gross margin, client performance, seller performance, and unit pricing across the entire procurement lifecycle.

The dashboard is built on a **star schema data model** (fact + dimension tables) covering revenue, gross margin, and unit pricing across product industries, clients, sellers, and geography — exposed through interactive, filterable visuals built entirely with DAX and Power Query.

---

## 🎯 Business Problem

Procurement teams need to answer questions like:
- Which clients, sellers, and product categories are driving revenue — and at what margin?
- Where are we losing money (negative-margin brands, low-margin sellers)?
- How does unit pricing vary by state, product, and purchase volume?
- How is revenue trending month-over-month, and is margin holding up?

This dashboard answers all of the above in a single, self-service Power BI report — no manual pivot tables, no static spreadsheets.

---

## 🖼️ Dashboard Preview

| Revenue Analysis | Client Analysis |
|---|---|
| ![Revenue Analysis](Images/Revenue_Analysis.png) | ![Client Analysis](Images/Client_Analysis.png) |

| Brand Analysis | SPO Unit Price Analysis |
|---|---|
| ![Brand Analysis](Images/Brand_Analysis.png) | ![SPO Unit Price Analysis](Images/SPO_Unit_Price_Analysis.png) |

| MIS Report |
|---|
| ![MIS Report](Images/MIS_Report.png) |

> 📌 *Add your exported screenshots to the `Images/` folder using the filenames referenced above (or update the paths to match your actual filenames).*

---

## ✨ Key Features

- **Executive KPI Header** — Gross Margin % and Total Revenue pinned across every page, with global Date and Brand filters
- **Revenue & Margin Trend (12-Month)** — combo chart tracking total revenue against gross margin % to spot seasonality and margin erosion
- **Revenue by State, Product Industry, Client & Seller** — ranked bar charts with margin % overlaid on each bar for instant profitability context
- **Negative Margin by Brands** — a dedicated diagnostic view surfacing loss-making brands for corrective action
- **SPO Unit Price Analysis** — Min / Avg / Max unit price tracking with a **Purchase Count > 3 toggle filter**, monthly price distribution, and state-level price benchmarking
- **Detail Matrix (Drill-down Table)** — client/seller/PO-level table with Purchase Count, Min/Max/Avg Unit Price, and Min/Max/Gross Margin %, expandable via `+` rows
- **MIS Report** — a routed-vs-ADHOC-vs-ARC margin comparison matrix by Product Industry and Client Group, built for finance reconciliation
- Fully interactive: every visual cross-filters the report on click, with date range and brand slicers applied globally

---

## 🧮 Data Model

Built on a **star schema** — a central fact table (transaction-level purchase order data: revenue, cost, quantity, unit price) connected to dimension tables for Client, Seller, Brand, Product Industry/Category, State, and Date.

All visuals anchor on dimension table columns for row context (Client, Seller, Brand, State, Product Industry), with every KPI — Revenue, Gross Margin %, Avg/Min/Max Unit Price — built as a DAX measure. This keeps the model clean and ensures numbers stay consistent no matter which dimension a visual slices by.

📄 See [`DAX/Measures.md`](DAX/Measures.md) and [`DAX/Calculated Columns.md`](DAX/Calculated Columns.md) for full DAX documentation.

---

## 🔧 Key DAX Techniques Used

- **Purchase-count filtering** — a toggle-driven measure (Purchase Count > 3) that dynamically excludes low-frequency SKUs from unit price analysis to avoid outlier distortion
- **Disconnected slicer** for margin-bracket filtering, resolved using `FILTER(VALUES(...))` and `REMOVEFILTERS()` to correctly manage filter context grain mismatches across visuals
- **Total Profit (Clean)** — a filtered measure architecture that separates genuine negative-margin transactions from cost-zero data quality issues, rather than letting bad data distort the Gross Margin % KPI
- **ADHOC vs. ARC vs. Routed margin comparison** — parallel measure sets in the MIS Report that reconcile margin performance across different order routing types

---

## 🛠️ Tools & Skills

`Power BI` · `DAX` · `Power Query` · `Star Schema Data Modeling` · `Row-Level Security (RLS)`

---

## 💡 Skills Demonstrated

- Data Modeling (Star Schema)
- DAX
- Time Intelligence
- Filter Context
- Power Query
- KPI Design
- Interactive Dashboard Development
- Procurement Analytics
- Performance Optimization

---

## 👤 Contact

**Aditya Singh**

- LinkedIn: *(www.linkedin.com/in/aditya-singhs)*
