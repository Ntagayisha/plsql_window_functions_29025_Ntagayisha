# plsql_window_functions_29025_Ntagayisha
# SQL JOINs & Window Functions Project

## Student Information
- Name: Ntagayisha Bienfait  
- Student ID: 29025  
- Course: INSY 8311 – Database Development with PL/SQL  
- Instructor: Eric Maniraguha  
- Group: B 

---

## Business Problem
This project analyzes sales data from a retail company in order to understand customer behavior, product performance, and sales trends.  
The goal is to support better business decisions using SQL JOINs and Window Functions.

---

## Database Schema and ER Diagram
The database contains three related tables: Customers, Products, and Sales.  
Customers and Products are connected through the Sales table using foreign keys.

### ER Diagram
![ER Diagram <img width="651" height="548" alt="my ER DIAGRAM VIEW" src="https://github.com/user-attachments/assets/fd3fbfca-88da-46a0-a256-d4af819796e5" />  
) OR ![MY ER DIAGRAM](https://github.com/user-attachments/assets/f10dad40-b829-45e5-bf93-56ad8a4ffc71)


---

## Part A: SQL JOINs
SQL JOINs were used to combine data from multiple tables and analyze relationships between customers, products, and sales transactions.

(Screenshots and explanations for JOINs are provided in the screenshots folder.)


---

## Part B: Window Functions Implementation

### 1. Ranking Window Functions
This query ranks customers based on their total spending using the RANK() window function.

![Ranking Customers] ![RANK 1 PL](https://github.com/user-attachments/assets/d464d16c-63d3-4780-a3af-c9ed52f575aa)


This helps identify the highest-value customers for business analysis.

---

### 2. Aggregate Window Functions
This query calculates a running total of sales quantities over time using the SUM() window function.

![Running Total]![AGGREGATES 2 PL](https://github.com/user-attachments/assets/308f0e8e-504a-4e5e-9de6-b5246622b4bc)

It shows how total sales accumulate progressively as time passes.

---

### 3. Navigation Window Functions
This query uses the LAG() function to compare current sales with previous sales.

![LAG Function]![PRODUCT COMPARISON 4 PL](https://github.com/user-attachments/assets/5357716c-2310-4c43-aee5-e4f6ef4c3696)


It helps analyze changes in sales between different time periods.

---

### 4. Distribution Window Functions
This query divides customers into four groups using the NTILE(4) function.

![NTILE Function]![QUARTILES 3 PL](https://github.com/user-attachments/assets/0da0da58-46dd-4315-bd04-3ffae86c55d4)


Customer segmentation supports targeted marketing and strategic planning.

---

## Results Analysis

### Descriptive Analysis – What happened?
The analysis shows how sales are distributed among customers and products, highlighting active and inactive records.

### Diagnostic Analysis – Why did it happen?
Sales differences are mainly caused by variations in customer purchasing frequency and product prices.

### Prescriptive Analysis – What should be done?
The company should focus on high-performing products and introduce promotions to re-engage inactive customers.

---
### My markdown for all queries from Db fiddle,
**Schema (PostgreSQL v18)**

    CREATE TABLE customers (
        customer_id SERIAL PRIMARY KEY,
        customer_name VARCHAR(50),
        region VARCHAR(50)
    );
    
    CREATE TABLE products (
        product_id SERIAL PRIMARY KEY,
        product_name VARCHAR(50),
        price DECIMAL(10,2)
    );
    
    CREATE TABLE sales (
        sale_id SERIAL PRIMARY KEY,
        customer_id INT REFERENCES customers(customer_id),
        product_id INT REFERENCES products(product_id),
        sale_date DATE,
        quantity INT
    );
    

---

**Query #1**

    INSERT INTO customers (customer_name, region) VALUES
    ('Bienfait', 'Kigali'),
    ('John', 'Kigali'),
    ('Mugisha', 'Southern'),
    ('Mucyo', 'Northern'),
    ('Manzi', 'Western');

There are no results to be displayed.

---
**Query #2**

    
    
    INSERT INTO products (product_name, price) VALUES
    ('Laptop', 800),
    ('Phone', 500),
    ('Tablet', 300),
    ('Printer', 250);

There are no results to be displayed.

---
**Query #3**

    
    
    INSERT INTO sales (customer_id, product_id, sale_date, quantity) VALUES
    -- INNER JOIN: retrieve sales with existing customers and products
    
    (1, 1, '2025-01-10', 1),
    (1, 2, '2025-02-12', 2),
    (2, 2, '2025-02-15', 1),
    (3, 3, '2025-03-01', 3),
    (4, 1, '2025-03-05', 1);

There are no results to be displayed.

---
**Query #4**

    
    
    -- RIGHT JOIN: find products with no sales activity
    SELECT 
        p.product_name,
        p.price
    FROM sales s
    RIGHT JOIN products p 
        ON s.product_id = p.product_id
    WHERE s.sale_id IS NULL;

| product_name | price  |
| ------------ | ------ |
| Printer      | 250.00 |

---
**Query #5**

    
    -- INNER JOIN: retrieve sales with existing customers and products
    SELECT 
        c.customer_name,
        c.region,
        p.product_name,
        s.quantity,
        s.sale_date
    FROM sales s
    INNER JOIN customers c 
        ON s.customer_id = c.customer_id
    INNER JOIN products p 
        ON s.product_id = p.product_id;

| customer_name | region   | product_name | quantity | sale_date  |
| ------------- | -------- | ------------ | -------- | ---------- |
| Bienfait      | Kigali   | Laptop       | 1        | 2025-01-10 |
| Bienfait      | Kigali   | Phone        | 2        | 2025-02-12 |
| John          | Kigali   | Phone        | 1        | 2025-02-15 |
| Mugisha       | Southern | Tablet       | 3        | 2025-03-01 |
| Mucyo         | Northern | Laptop       | 1        | 2025-03-05 |

---
**Query #6**

    
    -- LEFT JOIN: find customers with no sales
    SELECT 
        c.customer_name,
        c.region
    FROM customers c
    LEFT JOIN sales s 
        ON c.customer_id = s.customer_id
    WHERE s.sale_id IS NULL;

| customer_name | region  |
| ------------- | ------- |
| Manzi         | Western |

---
**Query #7**

    
    -- FULL OUTER JOIN: show customers and products including unmatched records
    SELECT 
        c.customer_name,
        p.product_name
    FROM customers c
    FULL OUTER JOIN products p
        ON c.customer_id = p.product_id;

| customer_name | product_name |
| ------------- | ------------ |
| Bienfait      | Laptop       |
| John          | Phone        |
| Mugisha       | Tablet       |
| Mucyo         | Printer      |
| Manzi         |              |

---
**Query #8**

    
    
    -- SELF JOIN: compare customers in the same region
    SELECT 
        a.customer_name AS customer_1,
        b.customer_name AS customer_2,
        a.region
    FROM customers a
    JOIN customers b
        ON a.region = b.region
       AND a.customer_id <> b.customer_id;

| customer_1 | customer_2 | region |
| ---------- | ---------- | ------ |
| Bienfait   | John       | Kigali |
| John       | Bienfait   | Kigali |

---
**Query #9**

    
       
    -- RANKING FUNCTION: rank customers based on total amount spent
    SELECT 
        c.customer_name,
        SUM(s.quantity * p.price) AS total_spent,
        RANK() OVER (ORDER BY SUM(s.quantity * p.price) DESC) AS spending_rank
    FROM sales s
    JOIN customers c ON s.customer_id = c.customer_id
    JOIN products p ON s.product_id = p.product_id
    GROUP BY c.customer_name;

| customer_name | total_spent | spending_rank |
| ------------- | ----------- | ------------- |
| Bienfait      | 1800.00     | 1             |
| Mugisha       | 900.00      | 2             |
| Mucyo         | 800.00      | 3             |
| John          | 500.00      | 4             |

---
**Query #10**

    
    -- AGGREGATE WINDOW FUNCTION: running total of quantities sold over time
    SELECT 
        sale_date,
        quantity,
        SUM(quantity) OVER (
            ORDER BY sale_date
            ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
        ) AS running_total_quantity
    FROM sales;

| sale_date  | quantity | running_total_quantity |
| ---------- | -------- | ---------------------- |
| 2025-01-10 | 1        | 1                      |
| 2025-02-12 | 2        | 3                      |
| 2025-02-15 | 1        | 4                      |
| 2025-03-01 | 3        | 7                      |
| 2025-03-05 | 1        | 8                      |

---
**Query #11**

    
    -- DISTRIBUTION FUNCTION: divide customers into four groups
    SELECT 
        customer_name,
        NTILE(4) OVER (ORDER BY customer_name) AS customer_quartile
    FROM customers;

| customer_name | customer_quartile |
| ------------- | ----------------- |
| Bienfait      | 1                 |
| John          | 1                 |
| Manzi         | 2                 |
| Mucyo         | 3                 |
| Mugisha       | 4                 |

---
**Query #12**

    
    
     -- NAVIGATION FUNCTION: compare current quantity with previous sale
    SELECT 
        sale_date,
        quantity,
        LAG(quantity) OVER (ORDER BY sale_date) AS previous_quantity
    FROM sales;

| sale_date  | quantity | previous_quantity |
| ---------- | -------- | ----------------- |
| 2025-01-10 | 1        |                   |
| 2025-02-12 | 2        | 1                 |
| 2025-02-15 | 1        | 2                 |
| 2025-03-01 | 3        | 1                 |
| 2025-03-05 | 1        | 3                 |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/iy53aavbyWuV4Ryv3P472c/0)

##ER Diagrams  


## Tools Used
- PostgreSQL (DB Fiddle)
- GitHub
- draw.io for ER Diagram creation

---

## References
- PostgreSQL Official Documentation  
- SQL Window Functions Tutorials  
- Course lecture materials  

---

## Academic Integrity Statement
“All sources were properly cited. Implementations and analysis represent original work. No AI generated content was copied without attribution or adaptation.”

---

## Professional Note
> “Whoever is faithful in very little is also faithful in much.” — Luke 16:10
