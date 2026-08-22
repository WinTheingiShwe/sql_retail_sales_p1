# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Database**: `sql_project_p2`

This project analyzes a retail sales dataset using SQL to demonstrate data cleaning, exploratory data analysis, and business-oriented data analysis.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

**Database**: PostgreSQL
- **Database Creation**: The project starts by creating a database named `sql_project_p2`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE sql_project_p2;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05'**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity > 4;
```

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT 
    category,
    SUM(total_sale) as total_sales,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY category;
```

4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000;
```

6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT 
    category,
    gender,
    COUNT(*) AS total_transactions
FROM retail_sales
GROUP 
    BY 
    category,gender
ORDER BY category;
```

7. **Write a SQL query to calculate the average sale for each month. Find the month with the highest average sales in each year.**:
```sql
SELECT 
    year,
    month,
    avg_sale
FROM 
(
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER(
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS sales_rank
    FROM retail_sales
    GROUP BY 1, 2
) AS monthly_sales
WHERE sales_rank = 1;
```

8.  **Write a SQL query to find the top 5 customers based on the highest total sales:**
```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY total_sales DESC
LIMIT 5;
```

9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
WITH hourly_sale AS (
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift;
```

## Findings

- **Category Performance**: Electronics generated the highest total sales at 313,810, followed by Clothing at 311,070 and Beauty at 286,840. Clothing recorded the highest number of orders with 701 transactions.
- **High-Value Transactions**: The dataset contains 306 transactions with a total sale amount greater than 1,000.
- **Sales Trends**: July 2022 recorded the highest average sales at 541.34, while March 2023 recorded the highest average sales at 535.53.
- **Customer Insights**: Clothing had the highest number of unique customers with 149, followed by Electronics with 144 and Beauty with 141. Customer 3 generated the highest total sales among all customers at 38,840.
- **Sales by Time of Day**: Evening had the highest number of orders with 1,062 transactions, followed by Morning with 558 and Afternoon with 377. This indicates that the evening was the busiest sales period in the dataset.

## Analysis Areas

- **Sales Performance**: Total sales and high-value transactions
- **Customer Analysis**: Top customers and unique customers by category
- **Category Analysis**: Sales and transaction analysis by product category
- **Monthly Sales Trends**: Average sales by month and year
- **Sales by Time of Day**: Order distribution across morning, afternoon, and evening

## Conclusion

This project demonstrates fundamental SQL skills for data analysis, including data cleaning, aggregation, filtering, subqueries, CTEs, and window functions.

## SQL Skills Demonstrated

- SELECT and filtering with WHERE
- Aggregate functions: COUNT, SUM, AVG
- GROUP BY and ORDER BY
- DISTINCT and COUNT(DISTINCT)
- CASE WHEN
- Common Table Expressions (CTEs)
- Subqueries
- Window functions and RANK()
- Date and time functions
- Data cleaning and NULL handling
  
## How to Use

1. **Clone the Repository**: Clone this project repository from GitHub.
2. **Prepare the Dataset**: Use `SQL - Retail Sales Analysis_utf.csv` as the retail sales dataset.
3. **Set Up the Database**: Create the `sql_project_p2` database and the `retail_sales` table in PostgreSQL.
4. **Load the Data**: Import the CSV dataset into the `retail_sales` table.
5. **Run the SQL Queries**: Execute `retail_sales_analysis.sql` in PostgreSQL to perform data cleaning and sales analysis.
6. **Explore and Modify**: Modify the queries to explore additional insights from the retail sales dataset.

## Acknowledgment

This project was developed as a learning project while following a SQL retail sales analysis tutorial. I recreated the analysis, practiced the SQL queries, and adapted the project for my portfolio.
## Author - Win Theingi Shwe

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!
Thank you for your support, and I look forward to connecting with you!
