# 📈 Shopify Sales & Customer Funnel Report

## 1. Background and Overview

**Business Problem:** The e-commerce strategy and marketing teams lack centralized visibility into the end-to-end customer purchasing funnel. Prior to this analysis, product inventory forecasting and regional ad targeting relied on fragmented reports rather than consolidated transactional data, leading to inefficient ad spend and missed retention opportunities.

**Objective:** To analyze Shopify sales data natively within a Business Intelligence environment to uncover hidden purchasing behaviors, map the customer retention funnel, and evaluate product-level profitability. The ultimate goal is to translate raw transactional data into actionable strategies that optimize payment gateway costs, improve the **Repeat Customer Rate**, and maximize **Customer Lifetime Value (LTV)**.

**Data Source:** [Data](https://docs.google.com/spreadsheets/d/1a6SfTlwWKhy1lKCKP85_DarD1KYzUeMe/edit?usp=drive_link&ouid=103120213960573396884&rtpof=true&sd=true)

**Tech Stack Used:** * **Power BI Desktop:** End-to-end solution environment.
* **Power Query (M):** Automated ETL (Extract, Transform, Load) pipelines, data cleaning, and anomaly handling.
* **DAX (Data Analysis Expressions):** Complex metric aggregations, time-intelligence calculations, and funnel modeling.
![image alt](https://github.com/Priyanshu110905/Shopify-Sales-Customer-Funnel-Report/blob/30e7acfce1294299181021f48934c347931bc258/Screenshot%202026-06-20%20133600.png)

---

## 2. Data Structure Overview

### Data Dictionary
Below are the core attributes driving the analysis. Extraneous identifiers were modeled out to optimize performance.

| Column Name | Data Type | Description (Business Context) |
| :--- | :--- | :--- |
| `Order Number` | Integer | Unique sequential identifier for tracking individual sales. |
| `Invoice Date` | Datetime | Timestamp of transaction finalization. Used for cohort and trend analysis. |
| `Product Type` | String | Category of item sold (e.g., Running Shoes, Tennis Shoes). |
| `Quantity` | Integer | Volume of units per line item. Drives inventory turnover metrics. |
| `Subtotal Price` | Float | Base revenue before taxes/shipping. Used for gross margin estimation. |
| `Total Price USD` | Float | Standardized financial metric for cross-border revenue reporting. |
| `Customer Id` | String | Unique buyer identifier, critical for calculating retention and LTV. |
| `Gateway` | String | Payment processor (e.g., `shopify_payments`, `paypal`). |

### Visual Data Cleaning (Power Query)
To ensure high data integrity, the raw dataset underwent rigorous cleaning exclusively using Power Query Editor before being loaded into the data model.

| Phase | Metric / Column | Before Cleaning (Raw) | After Cleaning (Power Query) | Business Impact |
| :--- | :--- | :--- | :--- | :--- |
| **System IDs** | `Admin GraphQL API ID` | `gid://shopify/LineItem/2153...` | **Removed** (Column dropped) | Reduced dataset size and memory load, speeding up BI refresh rates. |
| **Outliers** | `Quantity` | Included negative values (Refunds) | Filtered `> 0` via Custom Step | Prevented skewed **Average Order Value (AOV)** calculations. |
| **Standardization**| `Billing Address City` | Mixed case (e.g., `HOUSTON`, `houston`)| Format -> Capitalize Each Word | Enabled accurate geographic revenue mapping without duplicates. |
| **Null Handling** | `Gateway` | Blank/Null values present | Replaced with `"manual"` | Ensured 100% of revenue was attributed to a payment channel. |

---

## 3. Executive Summary

The analysis of **7,534 total items sold** generated high-impact insights into our revenue streams and customer lifecycle. The Power BI dashboard successfully translates fragmented data into a cohesive overview of business health.

**Key Quantified Results:**
* **Total Net Sales:** The dataset accounts for **$4.18M** in total net sales.
* **Customer Value:** The baseline Net Average Order Value (AOV) is highly lucrative at **$562.60**, resulting in an impressive overall Customer Lifetime Value (LTV) of **$943.60**.
* **Retention Success:** Out of **4,431 Total Customers**, **2,039** are returning buyers, yielding a strong **46% Repeat Customer Rate** and an average purchase frequency of **1.68**.
* **Product Dominance:** The **Running Shoes** category is our flagship driver, accounting for roughly **$1.5M** of total net sales, vastly outperforming Tennis and Walking shoes.

---

## 4. Insights Deep Dive

During the Exploratory Data Analysis (EDA) phase within Power BI, several profitable trends and operational bottlenecks were uncovered.

### Insight 1: Gateway Dependency and Processing Volume
A breakdown of payment methods reveals a heavy reliance on native systems. `shopify_payments` captured **58.45%** of the total net sales volume (**$2.44M**), followed by `amazon_payments` (**17.62%**) and `paypal` (**16.29%**). 

![image alt](https://github.com/Priyanshu110905/Shopify-Sales-Customer-Funnel-Report/blob/2e7e619ef1fdb498df93c64c77eb2e5382793bc2/Payment.png)

### Insight 2: Regional Revenue Concentrations
Geospatial EDA highlights that the United States market is heavily skewed toward specific urban centers. **Washington** is the undisputed leader in net sales, followed by Houston, New York City, and El Paso. 

![image alt](https://github.com/Priyanshu110905/Shopify-Sales-Customer-Funnel-Report/blob/2e7e619ef1fdb498df93c64c77eb2e5382793bc2/Region.png)

### Insight 3: Uncovering the Retention Funnel (DAX Logic)
To understand long-term value, it was critical to dynamically separate single-order customers from repeat buyers. By creating custom DAX measures, we isolated the **46% repeat rate**, allowing leadership to filter this retention metric dynamically by province or product type.

```dax
// DAX: Calculating the total number of Repeat Customers
// Identifies customers who have completed more than one transaction

Repeat Customer = 
VAR CustomerOrderCounts = 
    ADDCOLUMNS(
        VALUES(Sales[Customer Id]),
        "OrderCount", CALCULATE(DISTINCTCOUNT(Sales[Order Number]))
    )
RETURN
    CALCULATE(
        DISTINCTCOUNT(Sales[Customer Id]),
        FILTER(CustomerOrderCounts, [OrderCount] > 1)
    )

// Note: This measure dynamically responds to the Gateway and Province slicers on the dashboard.
```

### 5. Recommendations

Based strictly on the insights surfaced in the Power BI report, I recommend the following actionable business strategies:

* **1.Leverage Volume for Lower Payment Processing Fees:**
-Action: With $2.44M (58.45%) processed strictly through shopify_payments, the business has immense negotiating leverage. Approach Shopify Plus representatives to negotiate lower tiered processing rates based on this high transaction volume.
-Business Value: A fractional reduction in gateway fees (e.g., saving 0.2% per transaction) will drop directly to the bottom line, significantly improving gross margins on $4.18M in sales.

* **2.Geo-Targeted Ad Spend Reallocation:**
-Action: Shift top-of-funnel (ToFu) marketing budget away from underperforming lower-tier cities and concentrate ad spend aggressively on the top 4 markets: Washington, Houston, New York City, and El Paso.
-Business Value: By doubling down on regions with proven high Net Sales and Customer Purchase Behavior, the business will decrease Customer Acquisition Cost (CAC) and improve overall Return on Ad Spend (ROAS).

* **3.Capitalize on the "Running Shoes" Halo Effect:**
-Action: Since Running Shoes generate the highest revenue ($1.5M), create a post-purchase automated email sequence targeting these specific buyers to cross-sell lower-tier accessories or apparel.
-Business Value: Currently, the Purchase Frequency is 1.68. Incentivizing the massive Running Shoe customer base to make a second, smaller purchase will push the Repeat Customer Rate past the 50% mark and directly elevate the overall $943.60 LTV.
