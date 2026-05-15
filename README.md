# Amazon Sales Report — Power BI Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-Desktop-F2C811?style=flat&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-41%20Measures-7F77DD?style=flat)
![Status](https://img.shields.io/badge/Status-Complete-1D9E75?style=flat)
![Records](https://img.shields.io/badge/Records-128%2C976-185FA5?style=flat)

An end-to-end Business Intelligence dashboard built on Amazon India order-level sales data. Transforms 128,976 raw records into a 6-page interactive Power BI report covering sales trends, product performance, fulfillment effectiveness, customer segmentation, and geographic distribution.

---

## Table of contents

- [Project overview](#project-overview)
- [Dataset](#dataset)
- [Key objectives](#key-objectives)
- [Dashboard pages](#dashboard-pages)
- [DAX measures](#dax-measures)
- [Data model](#data-model)
- [Calculated columns](#calculated-columns)
- [Key findings](#key-findings)
- [Tools & technology](#tools--technology)
- [Project structure](#project-structure)
- [Setup & usage](#setup--usage)
- [Screenshots](#screenshots)

---

## Project overview

This project analyses Amazon India sales data to deliver actionable business insights across 6 report pages. Built entirely in Power BI Desktop with 41 custom DAX measures, the dashboard supports dynamic filtering via 3 synced slicers (Date · Category · State), includes a cross-page navigation bar, a bookmark-based Reset filters button, and conditional formatting throughout.

| Metric | Value |
|---|---|
| Total revenue | ₹76,031,267 |
| Total orders | 1,20,229 |
| Total units sold | 1,16,364 |
| Average order value | ₹633 |
| Fulfillment rate | 82.5% |
| Cancellation rate | 14.3% |
| States covered | 30+ |
| Product categories | 9 |
| DAX measures | 41 |
| Report pages | 6 |

---

## Dataset

**Source:** Amazon India sales transaction data  
**File:** `Amazon Sale Report.csv`  
**Rows:** 128,976 orders  
**Columns:** 21 raw fields

| Column | Type | Description |
|---|---|---|
| `Order ID` | Text | Unique order identifier |
| `Date` | Date | Order date |
| `Status` | Text | Order fulfillment status |
| `Fulfilment` | Text | Amazon or Merchant |
| `Sales Channel` | Text | Platform channel |
| `ship-service-level` | Text | Expedited or Standard |
| `Category` | Text | Product category (T-shirt, Shirt, etc.) |
| `Size` | Text | Product size (XS → 6XL) |
| `Qty` | Integer | Quantity ordered |
| `Amount` | Decimal | Order value in INR |
| `ship-city` | Text | Destination city |
| `ship-state` | Text | Destination state |
| `ship-country` | Text | Destination country |
| `B2B` | Boolean | True if business order |
| `Courier Status` | Text | Courier delivery status |
| `currency` | Text | Currency code |
| `fulfilled-by` | Text | Fulfillment entity |

---

## Key objectives

| # | Objective | Description |
|---|---|---|
| 1 | Sales overview | Analyse revenue trends, MoM growth, and order volumes over time |
| 2 | Product analysis | Identify top-performing categories, size demand, and revenue share |
| 3 | Fulfillment analysis | Compare Amazon vs Merchant channel effectiveness and cancel rates |
| 4 | Customer segmentation | Segment B2B vs B2C by revenue, behaviour, and order patterns |
| 5 | Geographical analysis | Map sales distribution across Indian states and cities |
| 6 | Business insights | Surface actionable signals — risk, growth, and demand gaps |

---

## Dashboard pages

### Page 1 — Sales overview
Executive summary landing page. Answers *"how are we doing overall?"*

- **Visuals:** 5 KPI cards · Line + bar combo (revenue + MoM growth) · Area chart (YTD + Last 30 days) · Date slicer
- **Key numbers:** ₹76M revenue · 1.2L orders · ₹633 AOV · 82.5% fulfillment · 14.3% cancellation
- **Slicers:** Date range · Category · State — all 3 synced to pages 2–6

---

### Page 2 — Product analysis
Category and size performance. Answers *"what are we selling?"*

- **Visuals:** Horizontal bar (revenue by category) · Donut (category share %) · Stacked column (orders by category + size) · Treemap (category × size)
- **Key numbers:** T-shirt 49.9% share (₹37.9M) · Shirt 27.2% (₹20.7M) · M/L/XL = 50% of orders

---

### Page 3 — Fulfillment analysis
Operational health across channels. Answers *"where are we losing orders?"*

- **Visuals:** Donut (order status) · Grouped bar (Amazon vs Merchant) · Line + ref line (cancel rate trend) · 100% stacked bar (expedited vs standard)
- **Key numbers:** Amazon cancel 12.9% · Merchant cancel 17.5% · Expedited rate 68.9%

---

### Page 4 — Customer segmentation
B2B vs B2C breakdown. Answers *"who is buying?"*

- **Visuals:** Two donuts (B2B vs B2C revenue + orders) · Grouped bar (category by segment) · Scatter chart (revenue vs cancel rate by state)
- **Key numbers:** B2C 99.2% of revenue (₹75.4M) · B2B ₹617K across 872 orders

---

### Page 5 — Geographical analysis
Regional sales distribution. Answers *"where is revenue coming from?"*

- **Visuals:** Filled map (India choropleth) · Horizontal bar (top 10 states) · Treemap (state revenue proportion)
- **Key numbers:** Maharashtra ₹12.9M · Karnataka ₹10.2M · Top 5 states = 55% of revenue

---

### Page 6 — Business insights
Composite actionable signals. Answers *"what should we do next?"*

- **Visuals:** Bar + line combo (revenue + MoM growth by category) · Scatter quadrant (revenue vs cancel rate) · Matrix heatmap (category × state)
- **Key finding:** T-shirt and Shirt in "Fix urgently" quadrant — high revenue, high cancellation

---

## DAX measures

All 41 measures live in the `Amazon Sale Report` table, organised across 6 groups.

### Sales overview (7)
```dax
Total Revenue       = SUMX('Amazon Sale Report', [Amount] * [Qty])
Total Orders        = DISTINCTCOUNT('Amazon Sale Report'[Order ID])
Total Units Sold    = SUM('Amazon Sale Report'[Qty])
Average Order Value = DIVIDE([Total Revenue], [Total Orders], 0)
Avg Selling Price   = DIVIDE([Total Revenue], [Total Units Sold], 0)
Avg Daily Revenue   = AVERAGEX(VALUES('Amazon Sale Report'[Date]), [Total Revenue])
Peak Daily Revenue  = MAXX(VALUES('Amazon Sale Report'[Date]), [Total Revenue])
```

### Order status (7)
```dax
Shipped Orders       = CALCULATE([Total Orders], 'Amazon Sale Report'[Status] IN {"Shipped", "Shipped - Delivered to Buyer", "Shipped - Out for Delivery", "Shipped - Picked Up"})
Cancelled Orders     = CALCULATE([Total Orders], 'Amazon Sale Report'[Status] = "Cancelled")
Returned Orders      = CALCULATE([Total Orders], 'Amazon Sale Report'[Status] IN {"Shipped - Returned to Seller", "Shipped - Returning to Seller", "Shipped - Rejected by Buyer"})
Lost in Transit Orders = CALCULATE([Total Orders], 'Amazon Sale Report'[Status] = "Shipped - Lost in Transit")
Cancellation Rate %  = DIVIDE([Cancelled Orders], [Total Orders], 0) * 100
Return Rate %        = DIVIDE([Returned Orders], [Total Orders], 0) * 100
Fulfillment Rate %   = DIVIDE([Shipped Orders], [Total Orders], 0) * 100
```

### Fulfillment channel (8)
```dax
Amazon Fulfilled Revenue     = CALCULATE([Total Revenue], 'Amazon Sale Report'[Fulfilment] = "Amazon")
Merchant Fulfilled Revenue   = CALCULATE([Total Revenue], 'Amazon Sale Report'[Fulfilment] = "Merchant")
Amazon Fulfillment Share %   = DIVIDE(CALCULATE([Total Orders], [Fulfilment] = "Amazon"), [Total Orders], 0) * 100
Merchant Fulfillment Share % = DIVIDE(CALCULATE([Total Orders], [Fulfilment] = "Merchant"), [Total Orders], 0) * 100
Amazon Cancellation Rate %   = DIVIDE(CALCULATE([Cancelled Orders], [Fulfilment] = "Amazon"), CALCULATE([Total Orders], [Fulfilment] = "Amazon"), 0) * 100
Merchant Cancellation Rate % = DIVIDE(CALCULATE([Cancelled Orders], [Fulfilment] = "Merchant"), CALCULATE([Total Orders], [Fulfilment] = "Merchant"), 0) * 100
Expedited Orders             = CALCULATE([Total Orders], 'Amazon Sale Report'[ship-service-level] = "Expedited")
Expedited Rate %             = DIVIDE([Expedited Orders], [Total Orders], 0) * 100
```

### Customer segmentation (8)
```dax
B2B Revenue        = CALCULATE([Total Revenue], 'Amazon Sale Report'[B2B] = TRUE())
B2C Revenue        = CALCULATE([Total Revenue], 'Amazon Sale Report'[B2B] = FALSE())
B2B Revenue Share % = DIVIDE([B2B Revenue], [Total Revenue], 0) * 100
B2C Revenue Share % = DIVIDE([B2C Revenue], [Total Revenue], 0) * 100
B2C Orders         = CALCULATE([Total Orders], 'Amazon Sale Report'[B2B] = FALSE())
B2C Units Sold     = CALCULATE([Total Units Sold], 'Amazon Sale Report'[B2B] = FALSE())
B2C Average Order Value  = DIVIDE([B2C Revenue], [B2C Orders], 0)
B2C Cancellation Rate %  = DIVIDE(CALCULATE([Cancelled Orders], [B2B] = FALSE()), [B2C Orders], 0) * 100
```

### Product & geography (6)
```dax
Category Revenue Share % = DIVIDE([Total Revenue], CALCULATE([Total Revenue], ALL('Amazon Sale Report'[Category])), 0) * 100
Category Order Rank      = RANKX(ALL('Amazon Sale Report'[Category]), [Total Orders], , DESC, DENSE)
Size Order Share %       = DIVIDE([Total Orders], CALCULATE([Total Orders], ALL('Amazon Sale Report'[Size])), 0) * 100
State Revenue Rank       = RANKX(ALL('Amazon Sale Report'[ship-state]), [Total Revenue], , DESC, DENSE)
State Revenue Share %    = DIVIDE([Total Revenue], CALCULATE([Total Revenue], ALL('Amazon Sale Report'[ship-state])), 0) * 100
Active States            = DISTINCTCOUNT('Amazon Sale Report'[ship-state])
```

### Time intelligence (5)
```dax
Revenue Previous Month = CALCULATE([Total Revenue], DATEADD(LocalDateTable[Date], -1, MONTH))
Revenue MoM Growth %   = DIVIDE([Total Revenue] - [Revenue Previous Month], [Revenue Previous Month], 0) * 100
Revenue YTD            = CALCULATE([Total Revenue], DATESYTD(LocalDateTable[Date]))
Revenue Last 30 Days   = CALCULATE([Total Revenue], DATESINPERIOD(LocalDateTable[Date], LASTDATE([Date]), -30, DAY))
Orders MTD             = CALCULATE([Total Orders], DATESMTD(LocalDateTable[Date]))
```

---

## Data model

```
Amazon Sale Report (fact)
│
├── [Date] ──────────────────────────────► LocalDateTable[Date]   (active, many-to-one)
│                                           Date · Year · MonthNo · Month
│                                           QuarterNo · Quarter · Day
│
└── [Date] ──── (inactive) ──────────────► DateTableTemplate[Date]
                                            (auto-generated, hidden)
```

**Relationship:** Single fact table model. The `LocalDateTable` is the date spine used by all time intelligence measures. No additional dimension tables — all segmentation is done via calculated columns and DAX `CALCULATE` filters.

---

## Calculated columns

Added directly to `Amazon Sale Report` table:

| Column | Type | Expression |
|---|---|---|
| `Year` | Integer | `YEAR([Date])` |
| `Month` | Integer | `MONTH([Date])` — number 1–12 |
| `Day` | Integer | `DAY([Date])` — day of month |
| `B2C` | Boolean | `NOT([B2B])` — inverse of B2B flag |
| `Row Number` | Integer | Sequential 1-based index via Power Query |

> **Note:** `Row Number` is best created in Power Query (`Table.AddIndexColumn`) rather than DAX to avoid recalculation overhead on 128,976 rows.

---

## Key findings

### 1. Revenue concentration
- T-shirt (49.9%) and Shirt (27.2%) together drive **77% of total revenue**
- Top 3 categories account for **91.1%** of revenue — heavy concentration risk

### 2. Cancellation problem
- 14.3% cancellation rate = **17,166 lost orders** worth an estimated **₹10.9M**
- Merchant channel cancels at **17.5%** vs Amazon's **12.9%** — a 4.6pp gap
- T-shirt and Shirt both sit in the "Fix urgently" quadrant (high revenue + high cancel)

### 3. Geographic dominance
- Maharashtra (₹12.9M) and Karnataka (₹10.2M) = **30.5% of total revenue**
- Top 5 states drive **55% of revenue** from ~44% of orders
- North-East and Central India are significantly under-served

### 4. Customer mix
- B2C drives **99.2%** of revenue (₹75.4M) — B2B at only ₹617K
- B2B is an untapped growth lever — bulk apparel orders for Blazzer and Shirt could be high margin

### 5. Shipping preference
- **68.9% of customers** choose Expedited shipping over Standard
- Expedited drives ₹54.7M vs Standard ₹21.3M — strong preference for fast delivery

### 6. Size demand
- M, L, and XL account for **50% of all orders**
- Sizes 4XL, 5XL, 6XL have very low demand — candidates for catalogue reduction

---

## Tools & technology

| Tool | Purpose |
|---|---|
| Power BI Desktop | Report building, visualisations, publishing |
| Power Query (M) | Data transformation, index reset, date column extraction |
| DAX | 41 custom measures + calculated columns |
| Bing Maps | Filled map (choropleth) for geographical analysis |
| MCP (Model Context Protocol) | Live model interrogation, measure deployment, DAX query execution |

---

## Project structure

```
amazon-sales-powerbi/
│
├── Amazon Sale Report.csv          # Raw source data (128,976 rows)
├── Amazon Sales Dashboard.pbix     # Power BI report file
├── README.md                       # This file
│
├── dax/
│   ├── sales_overview.dax          # Total Revenue, Orders, AOV, Units measures
│   ├── order_status.dax            # Fulfillment, Cancellation, Return measures
│   ├── fulfillment_channel.dax     # Amazon vs Merchant measures
│   ├── customer_segmentation.dax   # B2B / B2C measures
│   ├── product_geography.dax       # Category, Size, State ranking measures
│   └── time_intelligence.dax       # MoM, YTD, MTD, Last 30 Days measures
│
├── screenshots/
│   ├── p1_sales_overview.png
│   ├── p2_product_analysis.png
│   ├── p3_fulfillment.png
│   ├── p4_segmentation.png
│   ├── p5_geography.png
│   └── p6_business_insights.png
│
└── docs/
    └── data_dictionary.md          # Column descriptions and data types
```

---

## Setup & usage

### Prerequisites
- Power BI Desktop (latest version recommended — June 2023+ for Page Navigator visual)
- Windows OS (Power BI Desktop is Windows-only)

### Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/amazon-sales-powerbi.git
   cd amazon-sales-powerbi
   ```

2. **Open the report**
   - Launch Power BI Desktop
   - Open `Amazon Sales Dashboard.pbix`

3. **Refresh data** *(if the CSV path has changed)*
   - Go to `Home → Transform data → Data source settings`
   - Update the file path to point to `Amazon Sale Report.csv` on your machine
   - Click `Home → Refresh`

4. **Navigate the report**
   - Use the **top navigation bar** to move between the 6 pages
   - Use the **3 slicers** (Date · Category · State) on Page 1 to filter all pages simultaneously
   - Click the **Reset** button in the nav bar to clear all filters at once
   - Click the **ⓘ** button on any page for a description of that page's purpose and visuals

### Slicer sync
All 3 slicers are synced across all 6 pages via `View → Sync slicers`. The slicers are only visible on Page 1 — they apply silently on pages 2–6.

---

## Screenshots

> Add screenshots of each page after publishing. Suggested naming: `p1_sales_overview.png` through `p6_business_insights.png`. Place in the `/screenshots` folder and reference here.

| Page | Preview |
|---|---|
| P1 Sales overview | [screenshots/p1_sales_overview.png](https://github.com/paulavi700/Amazon_data_analysis-/issues/1#issue-4455078899) |
| P2 Product analysis | `screenshots/p2_product_analysis.png` |
| P3 Fulfillment | `screenshots/p3_fulfillment.png` |
| P4 Segmentation | `screenshots/p4_segmentation.png` |
| P5 Geography | `screenshots/p5_geography.png` |
| P6 Business insights | `screenshots/p6_business_insights.png` |

---

## Contributing

Pull requests are welcome. For major changes, open an issue first to discuss what you would like to change.

---

## License

This project is licensed under the MIT License.

---

*Built with Power BI Desktop · DAX · Power Query*
