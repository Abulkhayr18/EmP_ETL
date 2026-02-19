# BigP — Employee and Sales ETL Pipeline

A Python-based ETL (Extract, Transform, Load) project that extracts employee and sales data, applies transformations using pandas, and loads the results into a Databricks data warehouse.

---

## Project Overview

This project demonstrates a complete ETL pipeline built with Python and pandas. It works with two datasets — an employee dataset and a sales dataset — applies a series of data transformations, and loads the final data into Databricks Delta tables for querying and analysis.

---

## Project Structure

```
your-project-folder/
├── BigP.ipynb        # Main notebook containing all ETL logic
├── .env              # Secret credentials (never share or commit this file)
├── .gitignore        # Tells Git to ignore the .env file
└── README.md         # Project documentation
```

---

## Datasets

### Employees
Contains 10 records with the following columns after transformation:

| Column | Type | Description |
|---|---|---|
| EMPLOYEE_ID | Integer | Unique identifier for each employee |
| NAME | String | Full name of the employee |
| DEPARTMENT | String | Department the employee belongs to |
| AGE | Integer | Age of the employee |
| SALARY | Integer | Base salary |
| YEARS_OF_EXPERIENCE | Integer | Total years of work experience |
| CITY | String | City of employment |
| SalaryWithTax | Float | Salary with 20% tax applied |

### Sales
Contains 8 records with the following columns:

| Column | Type | Description |
|---|---|---|
| OrderID | String | Unique order identifier |
| Product | String | Name of the product sold |
| Quantity | Integer | Number of units sold |
| Price | Integer | Unit price |
| Region | String | Sales region |
| SalesRep | String | Name of the sales representative |

---

## Transformations Applied

The following transformations were applied to the employees dataset before loading:

- Column names converted to uppercase
- Columns renamed for clarity: `EMPLOYEEID` to `EMPLOYEE_ID`, `YEARSEXPERIENCE` to `YEARS_OF_EXPERIENCE`
- New column `SalaryWithTax` added by applying a 20% tax multiplier to the `SALARY` column
- Data filtered and sliced using various `loc` and `iloc` operations for exploration
- String filtering applied using `.str.contains()`, `.str.startswith()`, and `.between()`

---

## Requirements

Install the required packages before running the notebook:

```bash
pip install pandas numpy python-dotenv databricks-sql-connector
```

---

## Environment Setup

This project uses a `.env` file to store all credentials securely. Never hardcode credentials directly in the notebook.

### Step 1 — Create a `.env` file in the same folder as the notebook

```
DATABRICKS_HOST=your-workspace.azuredatabricks.net
DATABRICKS_TOKEN=your_databricks_token_here
DATABRICKS_HTTP_PATH=/sql/1.0/warehouses/your_warehouse_id
```

Rules for the `.env` file:
- No quotes around values
- No spaces around the equals sign
- Each credential on its own line

### Step 2 — Add a `.gitignore` file

Create a file called `.gitignore` in the same folder and add the following line to prevent your credentials from being uploaded to version control:

```
.env
```

### Step 3 — How to get your Databricks credentials

- **DATABRICKS_HOST**: Databricks workspace → SQL Warehouses → your warehouse → Connection details tab → Server hostname
- **DATABRICKS_HTTP_PATH**: Same Connection details tab → HTTP path
- **DATABRICKS_TOKEN**: Profile icon (top right) → Settings → Developer → Access tokens → Generate new token

---

## How to Run

1. Open `BigP.ipynb` in Jupyter Notebook or JupyterLab
2. Run all cells from top to bottom in order
3. The final cell connects to Databricks and loads both tables

The notebook is structured in this order:

- Cell 1 to 3: Import libraries and create the raw DataFrames
- Cell 4 to 57: Data exploration and transformation operations
- Cell 58 to 70: Column renaming, uppercase conversion, and derived column creation
- Final cells: Load credentials from `.env` and load data into Databricks

---

## Loading to Databricks

The load step connects to Databricks using the SQL connector and performs the following:

- Creates the `employees` table if it does not already exist
- Creates the `sales` table if it does not already exist
- Inserts all rows from both DataFrames into their respective tables

After a successful load, both tables are accessible in Databricks under:

```
Catalog → hive_metastore → default → employees
                                   → sales
```

---

## Querying the Data in Databricks

Once loaded, open the Databricks SQL Editor and run queries such as:

```sql
-- View all employees
SELECT * FROM employees;

-- Average salary by department
SELECT DEPARTMENT, AVG(SALARY) AS avg_salary
FROM employees
GROUP BY DEPARTMENT
ORDER BY avg_salary DESC;

-- View all sales
SELECT * FROM sales;

-- Total revenue per product
SELECT Product, SUM(Quantity * Price) AS total_revenue
FROM sales
GROUP BY Product
ORDER BY total_revenue DESC;
```

---

## Security Notes

- All credentials are stored in the `.env` file and loaded at runtime using `python-dotenv`
- The `.env` file is excluded from version control via `.gitignore`
- Never paste tokens or passwords directly in the notebook code
- If a token is accidentally exposed, regenerate it immediately in Databricks under Settings → Developer → Access tokens

---

## Dependencies

| Package | Purpose |
|---|---|
| pandas | Data manipulation and transformation |
| numpy | Numerical operations |
| python-dotenv | Loading credentials from `.env` file |
| databricks-sql-connector | Connecting to and writing to Databricks |
