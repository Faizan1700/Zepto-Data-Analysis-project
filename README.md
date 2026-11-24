Zepto Inventory Analysis using SQL

An end-to-end SQL analysis of a quick-commerce grocery inventory dataset, built with PostgreSQL. The project covers the full analyst workflow: database setup, data import, exploratory data analysis, data cleaning, and business-focused querying to uncover insights on pricing, discounts, stock availability, revenue potential, and inventory weight.

Tech stack: PostgreSQL · SQL · pgAdmin / psql · CSV

Table of Contents
Project Overview
Business Objectives
Dataset
Project Workflow
Business Questions and Queries
Key Findings
Repository Structure
How to Run
Skills Demonstrated
Acknowledgements
Author
Project Overview

Quick-commerce platforms manage thousands of SKUs across many categories, each with its own pricing, discounting, and stock behaviour. This project simulates how a data analyst in an e-commerce or retail team takes a raw inventory file and turns it into insights the business can act on.

Starting from a raw CSV, the project builds a relational table, profiles and cleans the data, and answers eight business questions using SQL only.

Business Objectives
Set up an inventory database and load raw product data
Explore product categories, stock availability, and pricing patterns
Detect and fix data quality issues (invalid prices, duplicates, unit inconsistencies)
Identify deep-discount products and stock-out risks among high-value items
Estimate revenue potential by category
Segment inventory by weight to support storage and logistics planning
Dataset

The dataset (data/zepto_v2.csv) is a public product-level inventory file with 3,732 rows and 9 columns across 14 categories. Each row is one SKU (stock keeping unit). The same product name can appear several times because it may be listed in different pack sizes, weights, or discounts.

Column	Description
sku_id	Synthetic primary key generated in the database
category	Product category (Fruits & Vegetables, Munchies, etc.)
name	Product name as listed in the app
mrp	Maximum retail price (stored in paise, converted to rupees)
discountPercent	Discount applied on MRP
availableQuantity	Units available in inventory
discountedSellingPrice	Final price after discount (converted to rupees)
weightInGms	Product weight in grams
outOfStock	Boolean flag for stock availability
quantity	Units per package
Project Workflow
1. Database and Table Creation

A zepto table is created with suitable data types (SERIAL primary key, NUMERIC for prices, BOOLEAN for stock status).

2. Data Import

The CSV is loaded using the pgAdmin import tool, or with \copy in psql. If a UTF-8 encoding error appears, save the CSV as UTF-8 before importing.

3. Exploratory Data Analysis
Total record count and sample rows
Null value checks across every column
List of distinct categories
In-stock vs out-of-stock product counts
Product names that appear under multiple SKUs
4. Data Cleaning
Identified and removed the row with an MRP of zero
Converted mrp and discountedSellingPrice from paise to rupees
5. Business Analysis

Eight SQL queries using aggregation, filtering, GROUP BY, CASE, and sorting. Full code is in sql/zepto_sql_data_analysis.sql.

Business Questions and Queries
#	Business question	Key logic
1	Top 10 products by discount percentage	ORDER BY discountPercent DESC LIMIT 10
2	High-MRP products (above ₹300) that are out of stock	WHERE outOfStock = TRUE AND mrp > 300
3	Estimated revenue per category	SUM(discountedSellingPrice * availableQuantity)
4	Products with MRP above ₹500 but discount below 10%	Filter on mrp and discountPercent
5	Top 5 categories by average discount	AVG(discountPercent) with LIMIT 5
6	Price per gram for products of 100 g and above	discountedSellingPrice / weightInGms
7	Weight segments: Low, Medium, Bulk	CASE on weightInGms (under 1 kg, under 5 kg, above)
8	Total inventory weight per category	SUM(weightInGms * availableQuantity)

Example query (Q3, estimated revenue by category):

sql
SELECT category,
       SUM(discountedSellingPrice * availableQuantity) AS total_revenue
FROM zepto
GROUP BY category
ORDER BY total_revenue;
Key Findings
The dataset has 3,732 SKUs; one invalid zero-price row was removed, leaving 3,731 clean records.
About 12% of SKUs (453 items) are marked out of stock.
The deepest discount in the data is 51%, seen on a group of wafer products. Several dairy and sauce items sit at 50%.
Only 4 distinct products priced above ₹300 are out of stock, so stock-out risk on premium items is low.
Fruits & Vegetables has the highest average discount (about 15.5%), followed by Meats, Fish & Eggs (about 11%).
39 distinct products have an MRP above ₹500 with a discount under 10%, which marks them as premium items with little price promotion.
Weight segmentation shows most of the catalogue is under 1 kg (1,613 Low), with 147 Medium and only 22 Bulk products.
Repository Structure
zepto-sql-data-analysis-project/
├── data/
│   └── zepto_v2.csv                    # Raw inventory dataset
├── sql/
│   └── zepto_sql_data_analysis.sql     # Table creation, EDA, cleaning, analysis
├── LICENSE
└── README.md
How to Run

Prerequisites: PostgreSQL, plus pgAdmin or the psql command-line client.

Clone this repository to your machine and open the project folder.
Create a new PostgreSQL database.
Run the table creation section of sql/zepto_sql_data_analysis.sql.
Import the data using the pgAdmin import tool, or run this from psql in the project root:
sql
\copy zepto(category,name,mrp,discountPercent,availableQuantity,discountedSellingPrice,weightInGms,outOfStock,quantity)
FROM 'data/zepto_v2.csv'
WITH (FORMAT csv, HEADER true, DELIMITER ',', QUOTE '"', ENCODING 'UTF8');
Run the exploration, cleaning, and analysis sections in order.
Skills Demonstrated
Relational table design and data loading in PostgreSQL
Exploratory data analysis with SQL
Data cleaning and unit transformation
Aggregations, GROUP BY, HAVING, and CASE logic
Turning business questions into queries
Pricing, inventory, and revenue analysis
Acknowledgements

The dataset comes from a public Kaggle inventory dataset originally scraped from Zepto product listings. This project builds on an open-source, MIT-licensed SQL project, and the original license is kept in this repository. The documentation, structure, and findings in this README are my own write-up.

Author

Md Faizan Data Analyst | SQL | PostgreSQL

LinkedIn: Md Faizan

If you found this project useful, consider giving it a star.
