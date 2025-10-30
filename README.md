# ET_Exam_Allan_095

## 1. Project Overview

This project demonstrates a full **Extract and Transform (ET)** pipeline using the **Online Retail** dataset. The goal is to:
- Download and extract raw transactional data.
- Create a smaller incremental dataset (e.g., latest month of records).
- Perform comprehensive data quality assessment.
- Apply five key data transformations: cleaning, standardization, enrichment, deduplication, and validation.
- Generate cleaned and transformed outputs ready for downstream analysis or loading (ELT/ETL).
- Visualize key business insights from the transformed data.

The final outputs are saved in structured directories for reproducibility and clarity.

---

## 2. Data Source

- **Dataset**: [Online Retail Dataset](https://archive.ics.uci.edu/ml/machine-learning-databases/00352/Online%20Retail.xlsx)  
  (Hosted by UCI Machine Learning Repository; also available on Kaggle)

- **Description**:  
  This is a transnational data set containing all the transactions occurring between 01/12/2010 and 09/12/2011 for a UK-based online retail store. It includes:
  - Over **540,000 rows**
  - Fields: `InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`, `CustomerID`, `Country`
  - Ideal for testing ETL workflows due to real-world data quality issues (missing values, duplicates, negative quantities, etc.)

---

## 3. ET Phases

###  Extract
- Downloaded the original `.xlsx` file from the UCI repository using `requests`.
- Converted and saved it as `raw_data.csv` in `ET_Exam_Allan_095/data/`.
- Created an **incremental subset** (`incremental_data.csv`) containing only transactions from the **most recent month** in the dataset.

###  Transform
Applied five core transformations to both `raw_data.csv` and `incremental_data.csv`:

1. **Data Cleaning**  
   - Filled missing `Description` with `"UNKNOWN"`.  
   - Replaced missing `CustomerID` with `-1` (using nullable integer type `Int64`).

2. **Data Standardization**  
   - Parsed and standardized `InvoiceDate` into ISO format (`YYYY-MM-DD HH:MM:SS`) in a new column `InvoiceDate_iso`.

3. **Data Enrichment**  
   - Added a derived column: `TotalPrice = Quantity × UnitPrice`.

4. **Data Deduplication**  
   - Removed exact duplicate rows.

5. **Data Validation**  
   - Filtered out records with negative `Quantity` or `UnitPrice` (invalid transactions).

Final transformed files:
- `transformed_full.csv`
- `transformed_incremental.csv`  
  (Saved in `ET_Exam_Allan_095/transformed/`)

### Load & Verification

After transformation, the cleaned datasets (`transformed_full.csv` and `transformed_incremental.csv`) were loaded into a **SQLite database** for structured storage, queryability, and downstream integration. SQLite was chosen for its simplicity, zero-configuration setup, and native support in Python—making it ideal for lightweight ETL verification in VS Code.

### Why SQLite?
- Lightweight and file-based (no server required)
- Fully compatible with pandas and VS Code (via extensions like *SQLite Viewer*)
- Enables SQL-based validation and sampling without external dependencies

### Verification Process
We used Python with `sqlite3` and `pandas` to:
1. Load both CSV files into separate tables in `exam_data.db`
2. Save the database to the `data/loaded/` directory
3. Run SQL queries to validate:
   - Table existence
   - Row counts
   - Column names and inferred types
   - Sample data integrity

#### Verification Code snippet
```python
# Verifying the load by running the sql query
db_path = r"C:\Users\user\ET_Exam_Allan_095\data\transformed\full_data.db"

# Connect to the database
conn = sqlite3.connect(db_path)
cursor = conn.cursor()

# Helper function to run and print query results
def run_query(description, query):
    print(f"\n {description}")
    print("-" * 50)
    cursor.execute(query)
    rows = cursor.fetchall()
    if rows:
        # Get column names
        col_names = [description[0] for description in cursor.description]
        print(" | ".join(col_names))
        print("-" * 50)
        for row in rows:
            print(" | ".join(str(value) for value in row))
    else:
        print("No results returned.")

# 1. Check table names
print(" Checking if expected tables exist...")
cursor.execute("SELECT name FROM sqlite_master WHERE type='table';")
tables = [row[0] for row in cursor.fetchall()]
print("Tables in database:", tables)

if "transformed_full" not in tables or "transformed_incremental" not in tables:
    print("  Warning: One or both tables are missing!")
else:
    # 2. Row counts
    run_query("Row count in transformed_full", "SELECT COUNT(*) AS row_count FROM transformed_full;")
    run_query("Row count in transformed_incremental", "SELECT COUNT(*) AS row_count FROM transformed_incremental;")

# Close connection
conn.close()
```
---

## 4. Tools Used

- **Python 3.x**
- **Pandas** – for data manipulation and transformation
- **Requests** – for downloading the dataset
- **Matplotlib** – for visualizations
- **Jupyter Notebook** (`.ipynb`) – for interactive development (e.g., `etl_extract.ipynb`, `etl_transform.ipynb`)
- **VS Code** – as the primary IDE

---

## 5. Steps to Run the Project

1. **Clone or create the project folder**:
   ```bash
   mkdir -p ET_Exam_Allan_095/data ET_Exam_Allan_095/transformed

## 6. Sample Visualizations
- After running the transformation notebook, you’ll see three key plots:
### 📈 Total Sales Over Time
![Total Sales Over Time](images/sales_over_time.png)

### 👑 Top 10 Customers by Total Sales
![Top 10 Customers](images/top_customers.png)

### 📦 Top 10 Products by Quantity Sold
![Top Products](images/top_products.png)