# SQL-PROJECTS
This repository contains all SQL projects completed
## E-Commerce Data Analysis
## Project Overview
> This project analyzes an E-commerce business database consisting of customers, products, and orders datasets. Using MySQL, we import, clean, optimize, and generate insightful business reports that could help management in decision-making.
##### The project covers:
+ Database creation and indexing
+ Data import and validation
+ Business-focused SQL queries
+ Performance optimization
+ Reporting insights
## Data Source: 
+ www.kaggle.com
## Tools Used:
#### My sql workbench: 
       1. was used to store customer, product, and order data.
       2. helped link the tables together and organize the database.
       3. was also used to run queries and find sales insights.
## Database Overview
##### Tables:
+ Customers
+ Products
+ Transaction
+ Orders
## Sample Business Questions Answered
I sampled the following business questions to explore and understand the dataset.
+ List top-selling products.
+ Find the total revenue generated.
+ Identify the most valuable customers.
+ Calculate the average order value.
+ Break down the total revenue by month.
+ Retrieve the purchase history for a specific customer.
+ Identify the top product categories by total revenue.
+ Count the number of repeat customers.
+ Calculate the customer churn rate.
+ Find the most popular products in a specific category.
+ Identify products that haven't been sold in the last three months.
+ Calculate the average transaction value for each customer.
+ Determine the percentage of total revenue contributed by the top 10 customers.
+ Identify the days with the highest number of sales.
+ Calculate the average number of products included in each order.
### SQL Queries
Use ecommerce;
#### 1. List top-selling products.

SELECT products.product_id, products.product_name, products.product_category, SUM(orders.quantity) AS total_sold  
FROM products  
JOIN orders ON products.product_id = orders.product_id  
GROUP BY products.product_id, products.product_name, products.product_category  
ORDER BY total_sold DESC  
LIMIT 10;

#### 2. Find the total revenue generated.

SELECT SUM(orders.quantity * products.product_price) AS total_revenue  
FROM orders   
JOIN products  ON orders.product_id = products.product_id;

#### 3. Identify the most valuable customers
SELECT customers.customer_id, CONCAT(customers.first_name, ' ', customers.last_name) AS customer_name,
       SUM(orders.quantity * products.product_price) AS total_spent  
FROM customers 
JOIN orders ON customers.customer_id = orders.customer_id  
JOIN products ON orders.product_id = products.product_id  
GROUP BY customers.customer_id, customer_name  
ORDER BY total_spent DESC  
LIMIT 10;

#### 4. Calculate the average order value.

SELECT SUM(orders.quantity * products.product_price) / COUNT(DISTINCT orders.order_id) AS avg_order_value  
FROM orders 
JOIN products ON orders.product_id = products.product_id;

#### 5. Break down the total revenue by month

SELECT 
    MONTH(orders.order_date) AS month,  
    YEAR(orders.order_date) AS year,  
    SUM(orders.quantity * products.product_price) AS total_revenue  
FROM orders   
JOIN products  ON orders.product_id = products.product_id  
GROUP BY YEAR(orders.order_date), MONTH(orders.order_date)  
ORDER BY year, month;

#### 6. Retrieve the purchase history for a specific customer.

SELECT 
    customers.customer_id,  
    CONCAT(customers.first_name, ' ', customers.last_name) AS customer_name,
    orders.order_id,  
    orders.order_date,  
    products.product_id,  
    products.product_name,  
    orders.quantity,  
    (orders.quantity * products.product_price) AS total_spent,  
    orders.payment_method  
FROM customers  
JOIN orders ON customers.customer_id = orders.customer_id  
JOIN products ON orders.product_id = products.product_id  
WHERE customers.customer_id  = 1 
ORDER BY orders.order_date DESC;

#### 7. Identify the top product categories by total revenue.

SELECT 
    products.product_category,  
    SUM(orders.quantity * products.product_price) AS total_revenue  
FROM orders 
JOIN products ON orders.product_id = products.product_id  
GROUP BY products.product_category  
ORDER BY total_revenue DESC  
LIMIT 10;

#### 8. Count the number of repeat customers.

SELECT COUNT(*) AS repeat_customers  
FROM orders  
JOIN Customers ON orders.customer_id = customers.customer_id 
GROUP BY orders.customer_id  
HAVING COUNT(orders.order_id) > 1;

#### 9. Calculate the customer churn rate.

WITH active_customers AS (
    SELECT DISTINCT customer_id FROM orders
    WHERE order_date >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)  -- Active in the last 6 months
),
all_customers AS (
    SELECT DISTINCT customer_id FROM orders
)
SELECT 
    (COUNT(all_customers.customer_id) - COUNT(active_customers.customer_id)) * 100.0 
    / COUNT(all_customers.customer_id) AS churn_rate
FROM all_customers
LEFT JOIN active_customers ON all_customers.customer_id = active_customers.customer_id;

#### 10. Find the most popular products in a specific category.

SELECT products.product_id, products.product_name, products.product_category, SUM(orders.quantity) AS total_sold
FROM products 
JOIN orders  ON products.product_id = orders.product_id
WHERE products.product_category = 'Electronics'  
GROUP BY products.product_id, products.product_name, products.product_category
ORDER BY total_sold DESC
LIMIT 10;

#### 11. Identify products that haven't been sold in the last three months.

SELECT products.product_id, products.product_name, products.product_category
FROM products 
LEFT JOIN orders ON products.product_id = orders.product_id 
    AND orders.order_date >= DATE_SUB(CURDATE(), INTERVAL 3 MONTH)
WHERE orders.product_id IS NULL;

#### 12. Calculate the average transaction value for each customer.

SELECT 
    customers.customer_id, 
    CONCAT(customers.first_name, ' ', customers.last_name) AS customer_name, 
   round(AVG(transaction.price),0) AS avg_transaction_value
FROM customers 
JOIN transaction ON customers.customer_id = transaction.customer_id
GROUP BY customers.customer_id, customer_name
ORDER BY avg_transaction_value DESC;

#### 13. Determine the percentage of total revenue contributed by the top 10 customers.

SELECT 
    customers.customer_id, 
    CONCAT(customers.first_name, ' ', customers.last_name) AS customer_name, 
    SUM(transaction.price) AS total_spent, 
    ROUND((SUM(transaction.price) / (SELECT SUM(price) FROM transaction)) * 100, 2) AS percentage_of_total_revenue
FROM customers 
JOIN transaction ON customers.customer_id = transaction.customer_id
GROUP BY customers.customer_id, customer_name
ORDER BY total_spent DESC
LIMIT 10;

#### 14. Identify the days with the highest number of sales.

SELECT 
    order_date, 
    COUNT(order_id) AS total_sales
FROM orders
GROUP BY order_date
ORDER BY total_sales DESC
LIMIT 10;

#### 15. Calculate the average number of products included in each order

SELECT 
    ROUND(AVG(product_count), 2) AS avg_products_per_order
FROM (
    SELECT order_id, COUNT(product_id) AS product_count
    FROM orders
    GROUP BY order_id
) AS order_product_counts;











