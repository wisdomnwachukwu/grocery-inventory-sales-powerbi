# Grocery Inventory & Sales Intelligence Dashboard

A Power BI dashboard for analyzing inventory levels, reorder requirements, product sales volume, inventory turnover, and supplier performance.

This project demonstrates an end-to-end Power BI workflow covering **data preparation, data-quality validation, DAX calculations, business analysis, and dashboard design**.

---

## Dashboard Preview

### Executive Overview

![Executive Overview](Dashboard%20Screenshots/executive-overview.png)

The Executive Overview provides a high-level view of inventory and sales performance, including:

- Total Products
- Total Stock
- Estimated Inventory Value
- Total Sales Volume
- Products Requiring Reorder
- Average Inventory Turnover
- Sales Volume by Category
- Inventory Value by Category
- Top 10 Products by Sales
- Stock Status

---

### Inventory Management

![Inventory Management](Dashboard%20Screenshots/inventory-management.png)

The Inventory Management page focuses on operational inventory decisions.

It provides:

- Products Requiring Reorder
- Total Reorder Quantity
- Out-of-Stock Products
- Stock Quantity by Category
- Reorder Quantity by Category
- Product-level reorder details
- Reorder Shortfall
- Supplier information
- Warehouse/location information

---

### Sales, Product & Supplier Performance

![Sales, Product & Supplier Performance](Dashboard%20Screenshots/sales-product-supplier.png)

This page explores relationships between product sales and inventory movement while providing product and supplier-level detail.

It includes:

- Sales Volume vs Inventory Turnover
- Average Inventory Turnover by Category
- Top 10 Suppliers by Sales Volume
- Product Performance table
- Category filtering

---

# Business Problem

Inventory-heavy businesses need to understand what products they have, what products require replenishment, how sales volume varies across categories, and which products or suppliers require further investigation.

The objective of this project was to transform a raw inventory and sales dataset into an interactive reporting solution that can help answer questions such as:

- How many products are currently being tracked?
- How much stock is available?
- Which products require replenishment?
- How much reorder quantity is associated with products requiring attention?
- Which product categories contribute the most sales volume?
- Which products have high sales volume?
- How does inventory turnover vary across categories?
- Which suppliers are associated with the highest sales volume?
- Which individual products require inventory attention?

---

# Project Objectives

The project was designed to:

1. Clean and validate the raw dataset.
2. Identify data-quality issues before analysis.
3. Create useful inventory-management classifications.
4. Build reusable DAX measures.
5. Analyze product, category, inventory, and supplier performance.
6. Design an interactive three-page Power BI dashboard.
7. Communicate business insights through clear visualizations.
8. Document assumptions and limitations rather than overstating what the dataset can support.

---

# Dataset

The project uses the **Grocery Inventory and Sales Dataset** from Kaggle.

**Source:** [Grocery Inventory and Sales Dataset](https://www.kaggle.com/datasets/salahuddinahmedshuvo/grocery-inventory-and-sales-dataset)

The dataset contains **990 product-level records** and includes fields such as:

| Field | Description |
|---|---|
| Product_ID | Product identifier |
| Product_Name | Product name |
| Category | Product category |
| Supplier_ID | Supplier identifier |
| Supplier_Name | Supplier name |
| Stock_Quantity | Current stock quantity |
| Reorder_Level | Inventory threshold for replenishment |
| Reorder_Quantity | Quantity associated with the source reorder information |
| Unit_Price | Unit price supplied by the dataset |
| Date_Received | Date the product was received |
| Last_Order_Date | Last order date |
| Expiration_Date | Product expiration date |
| Warehouse_Location | Location/address information |
| Sales_Volume | Aggregate product-level sales volume |
| Inventory_Turnover_Rate | Inventory turnover rate supplied by the dataset |
| Status | Source status field |

---

# Data Preparation

The raw CSV file was prepared using **Power Query** before being loaded into the Power BI model.

## Data Quality Checks

The following checks were performed:

- Verified column names and data types.
- Checked for missing values.
- Checked for duplicate Product IDs.
- Standardized inconsistent category values.
- Validated numeric columns.
- Validated date columns.
- Reviewed inventory-related business rules.
- Created a stock-status classification.
- Removed temporary diagnostic steps after validation.

## Data Type Validation

Fields were assigned appropriate data types, including:

- Text
- Whole number
- Decimal number
- Date

Correct data types were important for reliable calculations and visualizations.

## Missing Category Value

The original dataset contained a small number of blank category values.

The missing category for the affected product was classified as:

`Fruits & Vegetables`

This ensured that category-based analysis was complete.

## Category Standardization

During visual analysis, duplicate-looking category labels were identified.

Further inspection showed that the labels contained inconsistent text values.

The category values were standardized so that equivalent categories were represented consistently as:

`Fruits & Vegetables`

This prevented the same business category from appearing as separate categories in dashboard visuals.

## Duplicate Product Check

Product IDs were checked for duplicates to validate the product-level structure of the dataset.

No duplicate Product IDs were retained as part of the final analytical data.

---

# Business Rules

A calculated `Stock Status` classification was created using the current stock quantity and reorder level.

```Power Query
if [Stock_Quantity] <= [Reorder_Level]
then "Reorder Needed"
else "Stock OK"
```

The resulting classifications are:

| Status | Definition |
|---|---|
| Stock OK | Current stock is above the reorder level |
| Reorder Needed | Current stock is at or below the reorder level |
| Out of Stock | Current stock quantity is zero |

These statuses are intentionally treated as separate concepts.

A product can require replenishment without being completely out of stock.

---

# Data Model

This version of the project uses a **single-table analytical model**.

The source dataset is suitable for the project's current inventory-focused analysis, so a more complex relational model was not introduced unnecessarily.

The dataset's effective analytical grain is **product-level inventory records**, rather than individual sales transactions.

This distinction is important because the dataset contains an aggregate `Sales_Volume` field rather than a detailed transaction table.

---

# DAX

Several DAX measures were created to support the dashboard.

## Key Measures

### Total Products

```DAX
Total Products =
DISTINCTCOUNT(
    'Grocery_Inventory_and_sales_Dataset'[Product_ID]
)
```

Counts unique products.

---

### Total Stock

```DAX
Total Stock =
SUM(
    'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
)
```

Calculates the total stock quantity.

---

### Inventory Value

```DAX
Inventory Value =
SUMX(
    'Grocery_Inventory_and_sales_Dataset',
    'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
        * 'Grocery_Inventory_and_sales_Dataset'[Unit_Price]
)
```

Calculates an estimated inventory value by multiplying stock quantity by unit price for each product and summing the results.

`SUMX` was used because the calculation requires row-by-row multiplication before aggregation.

---

### Total Sales Volume

```DAX
Total Sales Volume =
SUM(
    'Grocery_Inventory_and_sales_Dataset'[Sales_Volume]
)
```

Calculates aggregate sales volume.

---

### Products to Reorder

```DAX
Products to Reorder =
CALCULATE(
    DISTINCTCOUNT(
        'Grocery_Inventory_and_sales_Dataset'[Product_ID]
    ),
    'Grocery_Inventory_and_sales_Dataset'[Stock Status] = "Reorder Needed"
)
```

Counts unique products whose stock is at or below the reorder level.

This measure demonstrates the use of `CALCULATE` to modify filter context.

---

### Average Inventory Turnover

```DAX
Average Inventory Turnover =
AVERAGE(
    'Grocery_Inventory_and_sales_Dataset'[Inventory_Turnover_Rate]
)
```

Calculates the average inventory turnover rate supplied by the dataset.

---

### Total Suppliers

```DAX
Total Suppliers =
DISTINCTCOUNT(
    'Grocery_Inventory_and_sales_Dataset'[Supplier_Name]
)
```

Counts unique supplier names.

The supplier name was used rather than `Supplier_ID` because the source IDs are unique at the product-record level and therefore do not represent a clean supplier-level identifier in this dataset.

---

### Reorder Shortfall

```DAX
Reorder Shortfall =
'Reorder_Level'
    - 'Stock_Quantity'
```

Calculates the difference between the reorder level and current stock quantity.

This metric helps identify products with a larger gap below their reorder threshold.

---

# Dashboard Structure

The dashboard contains three pages.

## 1. Executive Overview

Designed for high-level monitoring.

### Key questions

- What is the overall inventory position?
- How much stock is available?
- What is the estimated inventory value?
- How much sales volume is recorded?
- How many products require reorder?
- How does turnover vary overall?
- Which categories contribute the most sales volume?
- What proportion of products require reorder?

---

## 2. Inventory Management

Designed for operational inventory analysis.

### Key questions

- How many products require replenishment?
- How much reorder quantity is represented?
- Are any products completely out of stock?
- Which categories contain the largest stock quantities?
- Which categories have the largest reorder quantities?
- Which individual products require attention?
- How large is each product's reorder shortfall?

---

## 3. Sales, Product & Supplier Performance

Designed for deeper product and supplier analysis.

### Key questions

- How are sales volume and inventory turnover related?
- How does average turnover vary by category?
- Which products have high sales volume?
- Which suppliers are associated with higher sales volume?
- What are the inventory characteristics of individual products?

---

# Key Results

The dashboard provides several high-level observations from the dataset:

- **990 products** are represented in the dataset.
- **465 products** are classified as requiring reorder based on the defined stock-status rule.
- Approximately **47% of products** therefore fall at or below their reorder level.
- The dashboard records approximately **58K total sales volume**.
- The estimated inventory value based on stock quantity × supplied unit price is approximately **$332.65K**.
- The average supplied inventory turnover rate is approximately **50.15**.
- The dataset contains **350 distinct supplier names**.
- The dashboard identifies **0 products with zero stock quantity** at the time represented by the dataset.

These observations describe the dataset and should not be interpreted as current real-world business conditions.

---

# Dashboard Design Approach

The dashboard was designed around a simple reporting hierarchy:

**Executive Overview → Inventory Management → Sales & Performance**

The design uses:

- Light neutral dashboard backgrounds
- White visual cards
- Dark navy typography
- Blue as the primary analytical accent
- Green for positive/healthy inventory status
- Amber/orange for reorder warnings
- Red for critical inventory indicators
- Consistent spacing and alignment
- Focused visuals rather than unnecessary decoration

Visuals were selected according to the business question rather than simply for appearance.

Examples:

- **Cards** for important KPIs
- **Bar charts** for category and supplier comparisons
- **Donut chart** for stock-status composition
- **Scatter plot** for the relationship between sales volume and inventory turnover
- **Tables** for detailed operational investigation

---

# Data Limitations

Several limitations of the source dataset should be considered when interpreting the dashboard.

## 1. Product-level rather than transaction-level sales

The dataset contains `Sales_Volume`, but it does not provide a detailed sales transaction table.

Therefore, this project does not attempt to calculate:

- Daily sales
- Monthly sales trends
- Year-over-year sales growth
- Customer-level sales
- Transaction-level revenue
- Transaction-level profit

A future transactional dataset would be required for these analyses.

## 2. Inventory Value Interpretation

The dashboard calculates:

`Stock Quantity × Unit Price`

The dataset does not clearly establish whether `Unit_Price` represents purchase cost, selling price, or another pricing basis.

Therefore, the dashboard refers to this as an **estimated inventory value based on the supplied unit price**.

## 3. Inventory Turnover

The source dataset already provides `Inventory_Turnover_Rate`.

The project analyzes this supplied metric rather than independently calculating turnover from cost of goods sold and average inventory.

## 4. Supplier Analysis

Supplier analysis is limited to information available in the dataset, such as:

- Supplier name
- Product association
- Sales volume

The dataset does not provide sufficient information to evaluate:

- Supplier delivery reliability
- Lead time
- Fill rate
- Late deliveries
- Supplier quality
- Procurement cost performance

Therefore, the dashboard does not make supplier reliability claims.

## 5. Warehouse Location

`Warehouse_Location` contains location/address-style information.

The project uses this field as operational detail rather than treating it as a validated warehouse dimension or performing warehouse-performance analysis.

---

# Tools Used

- **Microsoft Power BI**
- **Power Query**
- **DAX**
- **Git**
- **GitHub**

AI-assisted review was also used during the project development process as a secondary perspective for:

- Dashboard UX review
- Data-quality considerations
- Visualization choices
- DAX reasoning
- Portfolio presentation

The dashboard logic and final decisions were reviewed rather than blindly generated by AI.

---

# Skills Demonstrated

### Data Preparation

- Data profiling
- Data-type validation
- Missing-value handling
- Duplicate checking
- Text standardization
- Conditional columns
- Business-rule validation

### Power BI

- Dashboard design
- Interactive filtering
- KPI cards
- Bar charts
- Donut charts
- Scatter plots
- Tables
- Conditional formatting
- Visual-level filtering
- Report layout and formatting

### DAX

- `SUM`
- `AVERAGE`
- `DISTINCTCOUNT`
- `SUMX`
- `CALCULATE`
- Filter context
- Calculated columns
- Conditional logic

### Business Analysis

- Inventory monitoring
- Reorder analysis
- Product performance
- Category comparison
- Supplier analysis
- Operational reporting
- Data limitation assessment

---

# Future Improvements

A production-oriented version of this solution could be extended beyond the current Power BI prototype.

Potential improvements include:

### Data Engineering

- Store source data in SQL Server
- Build staging and transformation layers
- Implement ETL/ELT pipelines
- Introduce automated data-quality checks
- Use incremental data loading
- Automate data refresh

### Data Modeling

Move from the current single-table model toward a dimensional model containing tables such as:

- Fact Sales
- Fact Inventory
- Dim Product
- Dim Supplier
- Dim Customer
- Dim Date
- Dim Store/Warehouse

### Advanced Analytics

Future versions could include:

- Sales trends over time
- Year-over-year analysis
- Profit and margin analysis
- Days of inventory remaining
- Reorder forecasting
- Demand forecasting
- Supplier lead-time analysis
- Product profitability
- Customer segmentation

### Engineering Stack

A more advanced implementation could use:

```text
Source Data
    ↓
Python / APIs
    ↓
SQL Server
    ↓
ETL / ELT
    ↓
Data Warehouse
    ↓
dbt / Spark
    ↓
Power BI
    ↓
Automated Business Reporting
```

This would transform the project from a Power BI reporting prototype into a broader data-engineering pipeline.

---

# Repository Structure

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
├── Power BI/
│   └── Grocery Inventory & Sales Dashboard.pbix
│
└── Documentation/
    └── DAX Measures.md
```

---

# Project Takeaway

This project was built to practice the complete analytical workflow rather than only dashboard visualization:

**Understand the data → Clean the data → Validate the data → Define business rules → Create DAX measures → Analyze → Visualize → Communicate insights → Document limitations**

The next stage of development is to move from static product-level inventory analysis toward **transactional data, dimensional modeling, SQL, Python, ETL/ELT pipelines, and automated reporting**.

---

## Author

**Wisdom Nwachukwu**

Aspiring Data Engineer | Power BI | SQL | Python | Data Analytics
