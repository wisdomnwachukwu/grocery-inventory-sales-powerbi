# Grocery Inventory & Sales Intelligence Dashboard

A professional Power BI dashboard designed to transform grocery inventory and sales data into actionable business insights for inventory management, product performance, and supplier analysis.

![Power BI Dashboard](Dashboard%20Screenshots/executive-overview.png)

---

## 📊 Project Overview

This project explores how Power BI can be used to transform raw inventory and sales data into a practical business intelligence solution.

The dashboard focuses on three major areas:

- Executive-level inventory and sales performance
- Inventory monitoring and reorder management
- Product, sales, and supplier performance

The project was developed as both a **data analytics portfolio project** and a prototype for the type of reporting solution that could be adapted for small and medium-sized businesses.

---

## 🎯 Business Problem

Businesses that manage physical inventory need clear visibility into:

- How much stock they currently hold
- Which products require replenishment
- How inventory value is distributed across categories
- Which products generate the highest sales volume
- How inventory turnover varies across categories
- Which suppliers contribute the most sales volume

When this information exists only in raw spreadsheets or CSV files, it can be difficult to identify operational priorities quickly.

This project demonstrates how raw inventory data can be transformed into an interactive dashboard that supports faster analysis and decision-making.

---

## 🚀 Project Objectives

The main objectives of this project were to:

1. Clean and prepare the raw dataset for analysis.
2. Validate data quality and identify potential data issues.
3. Create meaningful DAX measures for business KPIs.
4. Build an interactive Power BI dashboard.
5. Identify products requiring inventory replenishment.
6. Analyze sales volume across product categories.
7. Analyze inventory value across categories.
8. Examine product performance.
9. Analyze supplier sales performance.
10. Present the results using a professional business-dashboard design.

---

## 🗂️ Dataset

### Source

The dataset used in this project is the **Grocery Inventory and Sales Dataset** from Kaggle.

Source:

https://www.kaggle.com/datasets/salahuddinahmedshuvo/grocery-inventory-and-sales-dataset

### Dataset Size

- **990 product records**
- **16 columns**

### Main Fields

| Field | Description |
|---|---|
| Product_ID | Product identifier |
| Product_Name | Product name |
| Category | Product category |
| Supplier_ID | Supplier identifier |
| Supplier_Name | Supplier name |
| Stock_Quantity | Current stock quantity |
| Reorder_Level | Stock threshold for replenishment |
| Reorder_Quantity | Recommended reorder quantity |
| Unit_Price | Unit price provided in the dataset |
| Date_Received | Date the product was received |
| Last_Order_Date | Most recent order date |
| Expiration_Date | Product expiration date |
| Warehouse_Location | Location/address field provided in the dataset |
| Sales_Volume | Sales volume associated with the product |
| Inventory_Turnover_Rate | Inventory turnover rate provided by the dataset |
| Status | Product status |

---

# 🔄 Data Preparation

Data preparation was performed using **Power Query in Power BI**.

The cleaning process included the following steps.

### 1. Data Type Validation

Column data types were reviewed and assigned appropriately.

Examples:

- IDs → Text
- Product names → Text
- Categories → Text
- Quantities → Whole Number
- Unit Price → Decimal Number
- Dates → Date
- Inventory Turnover Rate → Decimal Number

### 2. Column Standardization

The original dataset contained a column named:

`Catagory`

This was corrected to:

`Category`

### 3. Missing-Value Investigation

The Category column initially contained a missing value.

The affected record was reviewed and the missing category was assigned based on the product information.

### 4. Category Standardization

Category values were checked for inconsistent text formatting that caused visually duplicated categories.

The values were standardized so that equivalent categories were represented consistently.

### 5. Duplicate Checking

Product IDs were checked for duplicate records.

No duplicate Product_ID records were retained in the final dataset.

### 6. Business-Rule Column

A conditional column called `Stock Status` was created.

The logic was:

- If Stock Quantity <= Reorder Level → `Reorder Needed`
- Otherwise → `Stock OK`

This allowed inventory risk to be analyzed directly in the dashboard.

### 7. Reorder Shortfall

A calculated column called `Reorder Shortfall` was created to show the difference between the reorder level and current stock.

This helps identify products with larger inventory gaps.

---

# 🧮 Data Model

This version of the project uses a **single-table analytical model**.

The dataset is primarily product-level inventory data rather than a transaction-level sales database.

This distinction is important because the `Sales_Volume` field represents an aggregate sales-volume value associated with each product rather than individual sales transactions.

For a production business intelligence system, the model could later be expanded into a proper star schema containing:

- Fact Sales
- Dimension Product
- Dimension Customer
- Dimension Supplier
- Dimension Date
- Dimension Store/Warehouse

---

# 📐 DAX Measures

Several DAX measures were created to support the dashboard.

## Total Products

```DAX
Total Products =
DISTINCTCOUNT(
    'Grocery_Inventory_and_sales_Dataset'[Product_ID]
)
```

Counts the number of unique products in the dataset.

---

## Total Stock

```DAX
Total Stock =
SUM(
    'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
)
```

Calculates the total quantity of products currently in stock.

---

## Inventory Value

```DAX
Inventory Value =
SUMX(
    'Grocery_Inventory_and_sales_Dataset',
    'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
        * 'Grocery_Inventory_and_sales_Dataset'[Unit_Price]
)
```

Calculates an estimated inventory value by multiplying each product's stock quantity by its unit price and then summing the results.

> **Note:** The dataset does not clearly establish whether `Unit_Price` represents acquisition cost or selling price, so this metric is presented as an estimated inventory value rather than a confirmed accounting valuation.

---

## Total Sales Volume

```DAX
Total Sales Volume =
SUM(
    'Grocery_Inventory_and_sales_Dataset'[Sales_Volume]
)
```

Calculates the total sales volume represented in the dataset.

---

## Products to Reorder

```DAX
Products to Reorder =
CALCULATE(
    DISTINCTCOUNT(
        'Grocery_Inventory_and_sales_Dataset'[Product_ID]
    ),
    'Grocery_Inventory_and_sales_Dataset'[Stock Status]
        = "Reorder Needed"
)
```

Counts the number of unique products whose stock quantity is at or below the defined reorder level.

`CALCULATE()` changes the filter context so that only products marked as `Reorder Needed` are included.

---

## Average Inventory Turnover

```DAX
Average Inventory Turnover =
AVERAGE(
    'Grocery_Inventory_and_sales_Dataset'[Inventory_Turnover_Rate]
)
```

Calculates the average inventory turnover rate provided in the source dataset.

The dashboard does not assign a specific time-based unit such as "times per year" because that information was not established by the source dataset.

---

## Total Suppliers

```DAX
Total Suppliers =
DISTINCTCOUNT(
    'Grocery_Inventory_and_sales_Dataset'[Supplier_Name]
)
```

Counts the distinct supplier names represented in the dataset.

---

## Reorder Shortfall

```DAX
Reorder Shortfall =
'Grocery_Inventory_and_sales_Dataset'[Reorder_Level]
    - 'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
```

Shows the difference between the reorder threshold and the current stock quantity.

A larger positive shortfall indicates a larger gap relative to the reorder level.

---

# 📊 Dashboard Structure

The dashboard contains three pages.

---

## 1. Executive Overview

![Executive Overview](Dashboard%20Screenshots/executive-overview.png)

The Executive Overview provides a high-level summary of inventory and sales performance.

### Key KPIs

- Total Products
- Total Stock
- Inventory Value
- Total Sales Volume
- Products to Reorder
- Average Inventory Turnover

### Main Visuals

- Sales Volume by Category
- Inventory Value by Category
- Top 10 Products by Sales
- Stock Status

### Purpose

This page provides a quick management-level view of the overall inventory position and sales performance.

---

## 2. Inventory Management

![Inventory Management](Dashboard%20Screenshots/inventory-management.png)

The Inventory Management page focuses on stock monitoring and replenishment.

### Key KPIs

- Products to Reorder
- Total Reorder Quantity
- Out-of-Stock Products

### Main Visuals

- Stock Quantity by Category
- Reorder Quantity by Category
- Products Requiring Reorder

### Operational Table

The reorder table provides product-level detail including:

- Product Name
- Category
- Stock Quantity
- Reorder Level
- Reorder Shortfall
- Reorder Quantity
- Supplier Name
- Warehouse Location

### Purpose

This page is designed to help identify products that require attention and provide more detailed information for inventory replenishment decisions.

---

## 3. Sales, Product & Supplier Performance

![Sales Product Supplier Performance](Dashboard%20Screenshots/sales-product-supplier.png)

This page examines relationships between product sales, inventory turnover, and supplier performance.

### Key KPI

- Total Suppliers

### Main Visuals

- Top 10 Suppliers by Sales Volume
- Average Inventory Turnover by Category
- Sales Volume vs Inventory Turnover
- Product Performance Table

### Product Performance

The product-level analysis includes:

- Product Name
- Category
- Sales Volume
- Inventory Turnover Rate
- Stock Quantity
- Unit Price

### Purpose

This page provides a deeper analytical view of product and supplier performance.

---

# 🔎 Key Business Insights

The dashboard highlights several notable observations from the dataset.

### 1. A significant portion of products require replenishment

The dashboard identifies:

**465 products requiring reorder out of 990 products.**

This represents approximately **47% of the products** in the dataset.

This indicates that inventory replenishment is an important area for further investigation.

---

### 2. Fruits & Vegetables has the highest sales volume

Among the categories shown in the dashboard, **Fruits & Vegetables** records the highest sales volume.

This provides a starting point for investigating demand patterns and inventory requirements within the category.

---

### 3. Inventory value differs substantially across categories

The Inventory Value by Category visual shows that inventory value is not distributed evenly across product categories.

This can help management identify categories that represent a larger portion of inventory investment.

---

### 4. Product sales performance varies considerably

The Top 10 Products by Sales visual shows significant differences in sales volume between products.

This provides an opportunity to investigate:

- High-demand products
- Low-demand products
- Stock availability
- Reorder requirements
- Inventory turnover

---

### 5. Supplier sales contribution varies

The Top 10 Suppliers by Sales Volume visual shows that suppliers contribute different levels of sales volume across the dataset.

This can be used as a starting point for deeper supplier analysis.

---

# ⚠️ Data Limitations

This project is intended for portfolio and analytical demonstration purposes. Several limitations should be considered.

### 1. Static Dataset

The dataset is a static Kaggle dataset rather than a live business database.

Therefore, the dashboard does not represent real-time inventory conditions.

### 2. Product-Level Sales Data

`Sales_Volume` is an aggregate product-level field.

The dataset does not provide individual sales transactions with:

- Transaction ID
- Transaction date
- Customer
- Quantity sold per transaction
- Revenue per transaction

Therefore, this project does not attempt to perform detailed transaction-level time-series analysis.

### 3. Inventory Turnover

The `Inventory_Turnover_Rate` field is supplied by the dataset.

It is analyzed as provided rather than independently recalculated from transaction-level cost of goods sold and average inventory.

### 4. Inventory Value Interpretation

Inventory Value is calculated using:

`Stock Quantity × Unit Price`

However, the source dataset does not clearly define whether Unit Price represents purchase cost or selling price.

Therefore, the metric should be treated as an estimated inventory value.

### 5. Warehouse Location

The `Warehouse_Location` field contains location/address-like values.

It was therefore treated as location detail rather than being interpreted as a verified list of warehouse facilities.

### 6. Supplier Reliability

The dataset does not contain sufficient information to calculate supplier reliability.

For example, it does not provide reliable supplier:

- Delivery lead time
- On-time delivery rate
- Fulfillment rate
- Order delay history

Therefore, supplier analysis in this project focuses on measurable fields such as sales volume and product information rather than unsupported reliability scores.

---

# 🛠️ Tools & Technologies

- **Microsoft Power BI**
- **Power Query**
- **DAX**
- **Git**
- **GitHub**
- **AI-assisted analytical review**

---

# 🤖 AI-Assisted Learning

AI was used as a learning and review tool rather than as a replacement for the analytical process.

The workflow involved:

1. Building and cleaning the dashboard.
2. Creating the DAX measures.
3. Designing the visuals.
4. Reviewing the dashboard with AI.
5. Evaluating suggested improvements.
6. Implementing relevant improvements manually.
7. Validating the final dashboard.

This approach helped explore areas such as:

- Dashboard usability
- Visual hierarchy
- KPI presentation
- Business-question framing
- Data-quality considerations
- DAX documentation
- Portfolio presentation

The goal was to use AI to improve analytical thinking and review quality rather than blindly accepting generated solutions.

---

# 🎨 Dashboard Design

The dashboard was designed around a clean business intelligence style using:

- Light neutral backgrounds
- White visual/card areas
- Dark navy typography
- Blue analytical visuals
- Green indicators for positive stock status
- Amber/orange indicators for inventory warnings
- Consistent spacing and alignment
- Clear visual hierarchy

The design prioritizes readability and business interpretation over decorative elements.

---

# 📁 Repository Structure

```text
grocery-inventory-sales-powerbi/
│
├── README.md
│
├── Dashboard Screenshots/
│   ├── executive-overview.png
│   ├── inventory-management.png
│   └── sales-product-supplier.png
│
├── Documentation/
│   └── DAX Measures.md
│
└── Power BI/
    └── Grocery Inventory & Sales Dashboard.pbix
```

---

# 🔮 Future Improvements

This project can be extended into a more complete business intelligence and data engineering solution.

Potential improvements include:

### Data Engineering

- SQL Server as the backend database
- Python-based data ingestion
- Automated ETL/ELT pipelines
- Data-quality validation
- Incremental data loading
- Scheduled refreshes

### Data Modeling

- Star schema
- Fact Sales table
- Product dimension
- Customer dimension
- Supplier dimension
- Date dimension
- Warehouse/Store dimension

### Analytics

- Revenue analysis
- Gross profit
- Profit margin
- Year-over-Year growth
- Month-over-Month growth
- Year-to-Date analysis
- Customer segmentation
- Product profitability
- Inventory aging
- Reorder forecasting
- Supplier lead-time analysis

### Automation

A future production version could follow a pipeline such as:

```text
Source Data
     ↓
Python / API / Files
     ↓
Data Validation
     ↓
SQL Server / Data Warehouse
     ↓
Transformation
     ↓
Power BI
     ↓
Automated Reporting
```

The project could eventually be expanded into an automated inventory intelligence system for small and medium-sized businesses.

---

# 📚 What I Learned

This project strengthened my practical understanding of:

- Power Query data cleaning
- Data-quality validation
- Missing-value handling
- Duplicate checking
- Text standardization
- Conditional columns
- DAX measures
- `SUMX()`
- `DISTINCTCOUNT()`
- `CALCULATE()`
- Filter context
- Calculated columns
- KPI design
- Visual selection
- Dashboard layout
- Business insight generation
- Data limitations
- Git and GitHub portfolio management

Most importantly, the project reinforced the idea that a good dashboard is not simply a collection of charts.

It should connect:

**Data → Analysis → Business Questions → Insights → Action**

---

# 👤 Author

**Wisdom Nwachukwu**

Agricultural & Bio-resource Engineering graduate transitioning into **Data Engineering and Business Intelligence**.

Interested in:

- Data Engineering
- Business Intelligence
- SQL
- Python
- Power BI
- Data Pipelines
- Cloud Data Platforms
- AI & Data Applications

---

## ⭐ Project

If you find this project useful, feel free to explore the repository and review the dashboard, DAX documentation, and data preparation process.
