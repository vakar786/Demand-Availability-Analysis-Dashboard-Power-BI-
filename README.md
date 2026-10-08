# Demand-Availability-Analysis-Dashboard-Power-BI-
Power BI dashboard analyzing product demand vs availability, with supply shortage, loss, profit and average availability. Data sourced from MySQL.
# Demand & Availability Analysis Dashboard (Power BI)

An interactive Power BI dashboard that analyzes product demand against availability and measures the resulting **supply shortage, loss and profit**. Data is stored in **MySQL** and imported into Power BI for cleaning, modeling and visualization.

---

## Objective

- Compare product demand with available stock
- Identify supply shortages and the loss they cause


---

## Data Source

- Data stored in a **MySQL** database (`prod` schema)
- Imported into Power BI using **Get Data > MySQL database**
- Tables used:
  - **Inventory dataset:** Order Date, Product ID, Availability, Demand
  - **Products:** Product ID, Product Name, Unit Price ($)
- Both tables were joined on `Product ID` (left join)

---

## Tools Used

| Tool | Purpose |
|---|---|
| MySQL | Data storage, Product ID corrections, table join |
| Power BI Desktop | Data modeling and dashboard |
| Power Query | Data cleaning and type changes |
| DAX | Measures and calculations |

---

## Data Preparation

- Corrected Product IDs using SQL `UPDATE` statements
- Joined inventory data with the Products table using a `LEFT JOIN`
- Renamed columns (for example `Order Date (DD/MM/YYYY)` to `Order_Date_DD_MM_YYYY`)
- Set correct data types for dates and numbers

```sql
CREATE TABLE prod.new_table AS
SELECT
    a.`Order Date (DD/MM/YYYY)` AS Order_Date_DD_MM_YYYY,
    a.`Availability`,
    a.`Product ID` AS product_id,
    a.`Demand`,
    b.`Product Name` AS product_name,
    b.`Unit Price ($)` AS unit_price
FROM prod.`prod+env+inventory+dataset` AS a
LEFT JOIN prod.products AS b
    ON a.`Product ID` = b.`Product ID`;
```

---

## Key Metrics

| Metric | Meaning |
|---|---|
| **Total Availability** | Sum of stock available across all days |
| **Total Demand** | Sum of units customers needed |
| **Supply Shortage** | Demand that could not be met (Demand - Availability, when demand is higher) |
| **Loss** | Revenue lost because of the shortage (Shortage x Unit Price) |
| **Profit** | As defined in the dashboard (update to match your formula) |
| **Average Availability per Day** | Total Availability / Number of Days |

---

## Key DAX Measures

```dax
Total Availability = SUM('Demand/availibility'[Availability])

Total Demand = SUM('Demand/availibility'[Demand])

Total Number of Days =
DISTINCTCOUNT('Demand/availibility'[Order_Date_DD_MM_YYYY])

Average Availability per Day =
DIVIDE([Total Availability], [Total Number of Days])

Supply Shortage =
SUMX(
    'Demand/availibility',
    MAX('Demand/availibility'[Demand] - 'Demand/availibility'[Availability], 0)
)

Loss =
SUMX(
    'Demand/availibility',
    MAX('Demand/availibility'[Demand] - 'Demand/availibility'[Availability], 0)
        * 'Demand/availibility'[unit_price]
)
```

---

## Dashboard Pages

1. **Overview:** KPI cards for total availability, total demand, supply shortage, loss, profit and average availability
2. **Product Analysis:** demand vs availability by product
3. **Trend Analysis:** availability and demand over time
4. **Slicers:** filter by product and date

---

## Project Structure

```
production-analysis-powerbi/
├── README.md
├── PowerBI/
│   └── production_analysis.pbix
├── Data/
│   └── inventory_dataset.csv
├── SQL/
│   └── queries.sql
└── Screenshots/
```

---

## How to Open

1. Download `production_analysis.pbix` from the `PowerBI` folder
2. Open it with **Power BI Desktop** (free)
3. To refresh the data, update the data source to point to your own MySQL server (**Home > Transform Data > Data source settings**), or load the CSV from the `Data` folder

---

## Author
Mohammad Vakar Hasan 
Electrical Engineering MANIT Bhopal
