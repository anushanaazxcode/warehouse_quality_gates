# Warehouse Pipeline with Quality Gates

## Project Overview

This project demonstrates a simple Data Warehouse pipeline that loads sales transaction data into a dimensional model while enforcing data quality checks before publishing data.

The main objective is to ensure that only valid and trusted data reaches the warehouse. If any quality check fails, the pipeline stops execution and prevents bad data from being loaded.

This project was built as a Data Engineering capstone project.

---

# Architecture

Source Data (CSV)
        |
        v
 Data Quality Tests
        |
        v
 Data Transformation
        |
        v
 Dimensional Model
        |
        v
 SQLite Data Warehouse

---

# Dataset

The source dataset contains sales transactions with the following fields:

| Column | Description |
|----------|-------------|
| order_id | Unique order identifier |
| product | Product name |
| category | Product category |
| quantity | Quantity sold |
| unit_price | Price per unit |

Example:

```csv
order_id,product,category,quantity,unit_price
1,Laptop,Electronics,2,55000
2,Mouse,Accessories,5,500
3,Keyboard,Accessories,3,1200
```

---

# Dimensional Model

The warehouse follows a Star Schema design.

## Dimension Table

### dim_product

Stores descriptive information about products.

| Column |
|----------|
| product_key |
| product |
| category |

---

## Fact Table

### fact_sales

Stores transaction measures.

| Column |
|----------|
| order_id |
| product_key |
| quantity |
| unit_price |
| total_amount |

---

# Star Schema

```text
                 dim_product
               +-------------+
               | product_key |
               | product     |
               | category    |
               +------+------+
                      |
                      |
                      |
               +------+------+
               |  fact_sales |
               +-------------+
               | order_id    |
               | product_key |
               | quantity    |
               | unit_price  |
               | total_amt   |
               +-------------+
```

A diagram image is also available in:

```text
docs/model_diagram.png
```

---

# Data Quality Gates

The pipeline validates data before loading it into the warehouse.

The run fails if any of the following conditions are detected:

## Test 1: Null Order IDs

Checks for missing order identifiers.

```python
df["order_id"].isnull().any()
```

---

## Test 2: Duplicate Order IDs

Ensures every order is unique.

```python
df["order_id"].duplicated().any()
```

---

## Test 3: Invalid Quantity

Quantity must be greater than zero.

```python
(df["quantity"] <= 0).any()
```

---

## Test 4: Invalid Unit Price

Unit price must be positive.

```python
(df["unit_price"] <= 0).any()
```

---

## Test 5: Empty Dataset

The source file must contain records.

```python
len(df) > 0
```

---

# Pipeline Workflow

The ETL pipeline performs the following steps:

1. Read source CSV file
2. Run data quality tests
3. Stop execution if any test fails
4. Create product dimension
5. Create sales fact table
6. Calculate total sales amount
7. Load tables into SQLite warehouse

---

# Project Structure

```text
warehouse-quality-gates/
│
├── data/
│   └── sales_data.csv
│
├── pipeline/
│   └── etl.py
│
├── tests/
│   └── test_quality.py
│
├── sql/
│   └── quality_tests.sql
│
├── docs/
│   ├── design.md
│   └── model_diagram.png
│
├── requirements.txt
│
└── README.md
```

---

# Technologies Used

- Python
- Pandas
- SQLite
- PyTest
- SQL
- GitHub

---

# Installation

Clone the repository:

```bash
git clone https://github.com/your-username/warehouse-quality-gates.git
```

Navigate to the project folder:

```bash
cd warehouse-quality-gates
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

# Running the Pipeline

Execute:

```bash
python pipeline/etl.py
```

Expected output:

```text
Pipeline completed successfully
```

---

# Running Tests

Execute:

```bash
pytest tests/
```

Expected output:

```text
5 tests passed
```

---

# Output

After a successful run:

### SQLite Database

```text
warehouse.db
```

### Tables Created

- dim_product
- fact_sales

---

# Future Improvements

Possible enhancements:

- Add date dimension
- Implement incremental loading
- Add automated CI/CD pipeline
- Add logging and monitoring
- Use PostgreSQL instead of SQLite
- Integrate Great Expectations for advanced data quality testing

---

# Author

Anusha Naaz 

Data Engineering Capstone Project
