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

### ✅ Extract
- Downloaded the original `.xlsx` file from the UCI repository using `requests`.
- Converted and saved it as `raw_data.csv` in `ET_Exam_Allan_095/data/`.
- Created an **incremental subset** (`incremental_data.csv`) containing only transactions from the **most recent month** in the dataset.

### 🔧 Transform
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