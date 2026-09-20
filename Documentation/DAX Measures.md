# DAX Measures

This document contains the key DAX measures used in the **Grocery Inventory & Sales Intelligence Dashboard**.

---

## 1. Total Products

```DAX
Total Products =
DISTINCTCOUNT(
    'Grocery_Inventory_and_sales_Dataset'[Product_ID]
)
```

### Purpose

Counts the number of unique products in the dataset.

`DISTINCTCOUNT` is used because the analysis is based on unique `Product_ID` values.

---

## 2. Total Stock

```DAX
Total Stock =
SUM(
    'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
)
```

### Purpose

Calculates the total quantity of stock across all products.

---

## 3. Inventory Value

```DAX
Inventory Value =
SUMX(
    'Grocery_Inventory_and_sales_Dataset',
    'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
        * 'Grocery_Inventory_and_sales_Dataset'[Unit_Price]
)
```

### Purpose

Calculates an estimated inventory value by multiplying stock quantity by unit price for each product and then summing the results.

### DAX Concept

`SUMX` is an iterator. It evaluates the multiplication row by row before adding the results together.

### Data Note

The source dataset does not clearly establish whether `Unit_Price` represents cost price or selling price. Therefore, this dashboard treats the result as an **estimated inventory value based on the supplied unit price**.

---

## 4. Total Sales Volume

```DAX
Total Sales Volume =
SUM(
    'Grocery_Inventory_and_sales_Dataset'[Sales_Volume]
)
```

### Purpose

Calculates the aggregate sales volume recorded in the dataset.

### Data Note

`Sales_Volume` is an aggregate product-level field rather than a transaction-level sales table. Therefore, this project does not present the data as individual dated sales transactions.

---

## 5. Products to Reorder

```DAX
Products to Reorder =
CALCULATE(
    DISTINCTCOUNT(
        'Grocery_Inventory_and_sales_Dataset'[Product_ID]
    ),
    'Grocery_Inventory_and_sales_Dataset'[Stock Status] = "Reorder Needed"
)
```

### Purpose

Counts unique products whose current stock is at or below the defined reorder level.

### DAX Concept

`CALCULATE` evaluates an expression under a modified filter context.

In this measure, the distinct product count is evaluated only for products where `Stock Status` equals `"Reorder Needed"`.

---

## 6. Average Inventory Turnover

```DAX
Average Inventory Turnover =
AVERAGE(
    'Grocery_Inventory_and_sales_Dataset'[Inventory_Turnover_Rate]
)
```

### Purpose

Calculates the average inventory turnover rate across the products in the dataset.

### Data Note

The source dataset already provides `Inventory_Turnover_Rate`. This project therefore analyzes the supplied rate rather than calculating turnover from transaction-level cost-of-goods-sold and average-inventory data.

---

## 7. Total Suppliers

```DAX
Total Suppliers =
DISTINCTCOUNT(
    'Grocery_Inventory_and_sales_Dataset'[Supplier_Name]
)
```

### Purpose

Counts the number of unique supplier names represented in the dataset.

### Data Quality Note

`Supplier_ID` was not used for this KPI because the source data contains supplier IDs that are unique at the product-record level. Counting Supplier IDs would therefore incorrectly return the number of product records rather than the number of distinct suppliers.

---

## 8. Reorder Shortfall

```DAX
Reorder Shortfall =
'Grocery_Inventory_and_sales_Dataset'[Reorder_Level]
    - 'Grocery_Inventory_and_sales_Dataset'[Stock_Quantity]
```

### Purpose

Calculates the gap between a product's reorder level and its current stock quantity.

A larger positive value indicates a larger gap below the reorder threshold.

This field is used to help prioritize products requiring attention.

---

# Supporting Power Query Logic

A calculated `Stock Status` column was created during data preparation:

```Power Query
if [Stock_Quantity] <= [Reorder_Level]
then "Reorder Needed"
else "Stock OK"
```

### Purpose

Classifies each product according to whether its current stock is above or at/below its reorder level.

## Stock Status Definitions

- **Stock OK:** Current stock is above the reorder level.
- **Reorder Needed:** Current stock is at or below the reorder level.
- **Out of Stock:** Current stock quantity is zero.

These are distinct concepts and are not treated as interchangeable.

---

# Key DAX Concepts Demonstrated

This project demonstrates the following DAX concepts:

- `SUM`
- `AVERAGE`
- `DISTINCTCOUNT`
- `SUMX`
- `CALCULATE`
- Filter context
- Iterators
- Calculated columns
- Conditional logic
- Business-rule-based measures
