# Data Analytics and KPI Dashboard Pipeline

This project conducts an end-to-end data analysis from raw operational datasets to advanced business insights. It encompasses data preparation, exploratory data analysis (EDA), KPI generation, RFM customer segmentation, revenue forecasting, and anomaly detection.

## 🚀 Running in Google Colab

This pipeline is designed to be executed sequentially in a Google Colab notebook environment. Follow these steps to set up and run the analysis:

### 1. Upload Datasets
Before running any code, you **must manually upload the following CSV files** to your Google Colab environment (usually into the default `/content/` directory). The execution will fail if these files are not present.

- `Sales_2015.csv`
- `Sales_2016.csv`
- `Sales_2017.csv`
- `Products.csv`
- `Product_Subcategories.csv`
- `Product_Categories.csv`
- `Customers.csv`
- `Territories.csv`
- `Calendar.csv`
*(Optional for Bonus Checkpoint): `Returns.csv`*

You can upload these files in Colab by clicking the **Files** icon on the left sidebar and using the "Upload to session storage" button.

### 2. Install Dependencies
Run the following command in a Colab cell to ensure all required libraries are installed:
```python
!pip install duckdb pandas pyspark matplotlib seaborn scikit-learn plotly
```

### 3. Execute the Code
Run the notebook cells sequentially. The code is structured into logical checkpoints:
- **Checkpoint 1 (Data Prep):** Loads the raw CSVs using DuckDB and Pandas, joins the tables based on the ERD, and generates a unified `Master_Dataset.csv`.
- **Checkpoint 2 (EDA & Cleaning):** Performs Exploratory Data Analysis using PySpark and Pandas. It handles missing values, visualizes distributions, standardizes dates/currencies, and exports `Master_Dataset_Cleaned.csv`.
- **Checkpoints 3 & 4 (Analytics & KPIs):** Queries the cleaned dataset using memory-based DuckDB to compute core KPIs, revenue trends, product/geographical analyses, and generates RFM metrics.
- **Checkpoint 5/Bonus (Advanced Analytics):** Performs machine learning implementations including K-Means clustering for customer segmentation, linear regression for 6-month revenue forecasting, Z-score anomaly detection, and plots several visual dashboards.

---

## 📊 Entity Relationship Diagram (ERD) Overview

The underlying data model connects multiple dimension tables to central fact tables (Sales and Returns) using a star/snowflake schema approach:
- **Sales Fact Table**: Connects to `Products` (`ProductKey`), `Customers` (`CustomerKey`), `Territories` (`TerritoryKey`), and `Calendar` (`OrderDate`, `StockDate`).
- **Products Dimension**: Connects upwards to `Product_Subcategories` which in turn connects to `Product_Categories` to provide granular product hierarchy analytics.
- **Returns Fact Table**: Functions similarly to Sales, mapping returned quantities back to the `Products`, `Territories`, and `Calendar` dimensions.

---

## 📌 Assumptions Made During Analysis

To ensure transparency and reproducibility, the following assumptions were made when developing this analysis pipeline:

1. **Environment Architecture**: It is assumed that the notebook has sufficient system memory (RAM) allocated in Google Colab to process the multi-year transactional datasets in-memory via Pandas and DuckDB.
2. **File Encoding**: All source CSV operational datasets are encoded in `latin1`.
3. **Data Integrity & Relationships**: 
   - Primary keys and foreign keys (`CustomerKey`, `ProductKey`, `TerritoryKey`, `ProductSubcategoryKey`) are assumed to be reliable for SQL `INNER JOIN` operations without significantly dropping orphan transactional records.
   - Any missing geographic data can be rolled up reliably to higher territory levels (e.g., Country or Continent).
4. **Calculated Fields**: 
   - `Revenue` is derived dynamically by multiplying `OrderQuantity` by `ProductPrice` (or `ListPrice`), assuming no separate pre-calculated discounting applies unless explicitly stated in a discounts table.
   - `Profit` is derived by subtracting the total cost (`ProductCost * OrderQuantity`) from the calculated Revenue.
5. **Data Formats & Cleaning**: 
   - Currency/Monetary columns (like `AnnualIncome`, `ProductPrice`) may arrive as strings containing text symbols (like `$` or `,`). The pipeline assumes these can be safely stripped and cast to floats.
   - Date fields (`OrderDate`, `StockDate`) come in varied, mixed formats (`MM-DD-YYYY`, `DD/MM/YYYY`, etc.). A robust parsing function is assumed to successfully standardize 99%+ of these dates to `YYYY-MM-DD` timestamps.
6. **Execution Order**: The data flow relies on sequential dependency. Checkpoint 2 relies on the `Master_Dataset.csv` created in Checkpoint 1. Checkpoint 4 relies on the `Master_Dataset_Cleaned.csv` created in Checkpoint 2.
7. **Predictive Modeling Validity**: For the linear regression 6-month forecast, we assume that historical monthly revenue trends encapsulate seasonality sufficiently for base predictions. For K-Means, missing recency/frequency/monetary features are negligible or easily scaled.

Links:
Folder (Demo video, ER Diagram, Documentation) : https://drive.google.com/drive/folders/1IuRGmeh1ZvO2cknDnVZrV8DYMlbT9ffy

Google Collab : https://colab.research.google.com/drive/1uRVzXF9y0lEjudG4eQ0pI3Uw91aOrM-_#scrollTo=5FK_-5KQxvqq
