```markdown
# Grocery Inventory & Sales Intelligence Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-Data%20Visualization-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Power Query](https://img.shields.io/badge/Power%20Query-Data%20Transformation-5A3E85?style=for-the-badge)
![DAX](https://img.shields.io/badge/DAX-Data%20Analysis-118DFF?style=for-the-badge)
![GitHub](https://img.shields.io/badge/GitHub-Portfolio%20Project-181717?style=for-the-badge&logo=github)

A Power BI inventory and sales intelligence dashboard designed to provide visibility into stock levels, reorder requirements, product performance, sales volume, supplier-associated sales volume, and inventory turnover.

---

# 📊 Project Overview

Inventory management is an important part of retail operations.

Limited visibility into stock levels can make it difficult to identify products that require replenishment, understand where inventory value is concentrated, and evaluate product-level performance.

This project transforms a grocery inventory dataset into an interactive Power BI reporting solution that combines:

- Inventory monitoring
- Reorder analysis
- Product performance analysis
- Sales volume analysis
- Supplier-associated sales analysis
- Inventory turnover analysis

The dashboard was designed from a business-question perspective rather than simply displaying charts from the source data.

---

# 🎯 Business Problem

A grocery business needs visibility into questions such as:

- How many products are being tracked?
- How much stock is currently available?
- What is the estimated value of the inventory?
- What sales volume is recorded in the dataset?
- Which products require replenishment?
- Which categories have higher sales volume?
- Where is inventory value concentrated?
- Which products have higher sales volume?
- How does inventory turnover vary across categories?
- Which suppliers are associated with higher sales volume?

The dashboard provides a centralized view of these questions.

---

# 🎯 Project Objectives

The main objectives of this project were to:

1. Clean and validate the source dataset using Power Query.
2. Create meaningful DAX measures for inventory and sales analysis.
3. Identify products requiring reorder.
4. Analyze stock and reorder quantities by category.
5. Analyze inventory value by category.
6. Analyze product sales volume and inventory turnover.
7. Compare supplier-associated sales volume.
8. Build an interactive multi-page Power BI dashboard.
9. Present business observations in a clear and professional format.
10. Document the analytical logic and assumptions used in the project.

---

# 🗂️ Dataset

## Source

The project uses the **Grocery Inventory and Sales Dataset** available on Kaggle.

Source:

https://www.kaggle.com/datasets/salahuddinahmedshuvo/grocery-inventory-and-sales-dataset

## Dataset Structure

The dataset contains **990 product records** and **16 columns**:

- `Product_ID`
- `Product_Name`
- `Catagory`
- `Supplier_ID`
- `Supplier_Name`
- `Stock_Quantity`
- `Reorder_Level`
- `Reorder_Quantity`
- `Unit_Price`
- `Date_Received`
- `Last_Order_Date`
- `Expiration_Date`
- `Warehouse_Location`
- `Sales_Volume`
- `Inventory_Turnover_Rate`
- `Status`

The source column `Catagory` was renamed to `Category` during data preparation.

---

# 📐 Data Grain

Understanding the grain of a dataset is important before building calculations or visualizations.

The dataset used in this project is primarily **product-level inventory data**.

It is not a transaction-level sales dataset.

The `Sales_Volume` field represents an aggregate value associated with the product records rather than individual sales transactions.

Therefore, this project does not claim to provide:

- Daily sales transactions
- Individual customer transactions
- Transaction-level revenue
- Detailed monthly sales trends
- Customer-level sales analysis

This distinction helped determine which analyses were appropriate for the dashboard.

---

# 🔄 Data Preparation

Power Query was used to inspect, clean, standardize, and prepare the source data before analysis.

## 1. Data Type Validation

Appropriate data types were assigned to the columns.

Examples include:

- Text for product and supplier identifiers
- Text for names, categories, and locations
- Whole numbers for stock and quantity fields
- Decimal numbers for prices and turnover rates
- Date types for date fields

---

## 2. Column Name Correction

The source column:

`Catagory`

was renamed to:

`Category`

This improved consistency and readability throughout the analysis.

---

## 3. Missing-Value Validation

Column quality checks were performed to identify missing or invalid values.

The `Category` column initially contained a small number of blank records.

The missing category was investigated and corrected based on the corresponding product information.

---

## 4. Category Standardization

The dataset contained visually similar category values caused by differences in the underlying text.

The category values were standardized so that equivalent categories were represented consistently.

This prevented the same category from appearing as separate categories in the dashboard.

---

## 5. Duplicate Validation

`Product_ID` was checked for duplicate records to validate product-level uniqueness.

---

## 6. Stock Status Classification

A conditional column called `Stock Status` was created.

The logic was:

```text
If Stock Quantity <= Reorder Level
→ Reorder Needed

Otherwise
→ Stock OK
