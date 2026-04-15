# SQL Cheat Sheet

---

## Table of Contents

- [1. SELECT & FROM](#1-select--from)
- [2. WHERE — Filtering](#2-where--filtering)
- [3. ORDER BY & LIMIT / TOP](#3-order-by--limit--top)
- [4. Aggregate Functions](#4-aggregate-functions)
- [5. GROUP BY & HAVING](#5-group-by--having)
- [6. JOINs](#6-joins)
- [7. Subqueries](#7-subqueries)
- [8. CTEs — Common Table Expressions](#8-ctes--common-table-expressions)
- [9. Window Functions](#9-window-functions)
- [10. CASE WHEN](#10-case-when)
- [11. NULL Handling](#11-null-handling)
- [12. String Functions](#12-string-functions)
- [13. Date Functions](#13-date-functions)
- [14. Quick Reference Card](#14-quick-reference-card)

---

## 1. SELECT & FROM

```sql
-- Select specific columns
SELECT first_name, last_name, city
FROM customers;

-- Select all columns
SELECT *
FROM customers;

-- Select with alias
SELECT
    first_name        AS name,
    order_total       AS total,
    order_date        AS date
FROM orders;

-- Select distinct values only
SELECT DISTINCT city
FROM customers;

-- Select with calculated column
SELECT
    product_name,
    price,
    quantity,
    price * quantity  AS line_total
FROM order_items;
```

> **Tip:** Always name your columns explicitly in production queries.
> `SELECT *` is fine for exploration but slow and fragile in real code.

---

## 2. WHERE — Filtering

```sql
-- Basic equality
SELECT * FROM customers
WHERE city = 'Toronto';

-- Not equal
SELECT * FROM orders
WHERE status <> 'Cancelled';

-- Multiple conditions
SELECT * FROM orders
WHERE city = 'Toronto'
  AND order_total > 500;

-- OR condition
SELECT * FROM customers
WHERE city = 'Toronto'
   OR city = 'Vancouver';

-- Range filter
SELECT * FROM orders
WHERE order_total BETWEEN 100 AND 500;

-- Date range
SELECT * FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';

-- List of values
SELECT * FROM products
WHERE category IN ('Electronics', 'Furniture', 'Books');

-- Pattern matching (LIKE)
SELECT * FROM customers
WHERE first_name LIKE 'A%';      -- starts with A
WHERE last_name  LIKE '%son';    -- ends with son
WHERE email      LIKE '%@gmail%';-- contains @gmail

-- NULL check
SELECT * FROM customers
WHERE email IS NULL;

SELECT * FROM customers
WHERE email IS NOT NULL;
```

> **LIKE wildcards:**
> `%` = any number of characters &nbsp;|&nbsp; `_` = exactly one character

---

## 3. ORDER BY & LIMIT / TOP

```sql
-- Sort ascending (A → Z, 1 → 100)
SELECT * FROM customers
ORDER BY last_name ASC;

-- Sort descending (Z → A, 100 → 1)
SELECT * FROM orders
ORDER BY order_total DESC;

-- Sort by multiple columns
SELECT * FROM orders
ORDER BY city ASC, order_total DESC;

-- SQL Server — top 10 rows
SELECT TOP 10 *
FROM orders
ORDER BY order_total DESC;

-- MySQL — limit to 10 rows
SELECT *
FROM orders
ORDER BY order_total DESC
LIMIT 10;

-- MySQL — skip first 10, get next 10 (pagination)
SELECT *
FROM orders
ORDER BY order_date DESC
LIMIT 10 OFFSET 10;
```

---

## 4. Aggregate Functions

```sql
SELECT
    COUNT(*)              AS total_orders,       -- count all rows
    COUNT(customer_id)    AS orders_with_customer,-- count non-null only
    SUM(order_total)      AS total_revenue,
    AVG(order_total)      AS average_order_value,
    MIN(order_total)      AS smallest_order,
    MAX(order_total)      AS largest_order,
    ROUND(AVG(order_total), 2) AS avg_rounded
FROM orders;
```

| Function | What it does |
|---|---|
| `COUNT(*)` | Counts every row including NULLs |
| `COUNT(col)` | Counts only non-NULL values in that column |
| `SUM(col)` | Adds up all values |
| `AVG(col)` | Average of all non-NULL values |
| `MIN(col)` | Smallest value |
| `MAX(col)` | Largest value |
| `ROUND(val, n)` | Round to n decimal places |

---

## 5. GROUP BY & HAVING

```sql
-- Revenue and order count per city
SELECT
    city,
    COUNT(*)          AS total_orders,
    SUM(order_total)  AS total_revenue,
    AVG(order_total)  AS avg_order_value
FROM orders
GROUP BY city
ORDER BY total_revenue DESC;

-- GROUP BY + HAVING (filter after grouping)
SELECT
    city,
    COUNT(*) AS total_customers
FROM customers
GROUP BY city
HAVING COUNT(*) > 100;       -- only cities with 100+ customers

-- WHERE vs HAVING
SELECT
    city,
    COUNT(*) AS orders
FROM orders
WHERE order_date >= '2024-01-01'    -- filters rows BEFORE grouping
GROUP BY city
HAVING COUNT(*) > 50;               -- filters groups AFTER grouping
```

> **Rule:** `WHERE` filters rows. `HAVING` filters groups.
> You cannot use an aggregate function inside `WHERE`.

---

## 6. JOINs

```sql
-- INNER JOIN — only matching rows in both tables
SELECT
    c.first_name,
    c.last_name,
    o.order_date,
    o.order_total
FROM customers c
INNER JOIN orders o
    ON c.customer_id = o.customer_id;

-- LEFT JOIN — all rows from left table + matches from right
SELECT
    c.first_name,
    c.last_name,
    o.order_total           -- NULL if customer has no orders
FROM customers c
LEFT JOIN orders o
    ON c.customer_id = o.customer_id;

-- Find customers who have NEVER ordered
SELECT c.first_name, c.last_name
FROM customers c
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
WHERE o.customer_id IS NULL;

-- RIGHT JOIN — all rows from right table + matches from left
SELECT c.first_name, o.order_total
FROM customers c
RIGHT JOIN orders o
    ON c.customer_id = o.customer_id;

-- Joining 3 tables
SELECT
    c.first_name,
    o.order_date,
    p.product_name,
    oi.quantity
FROM customers     c
INNER JOIN orders      o  ON c.customer_id  = o.customer_id
INNER JOIN order_items oi ON o.order_id     = oi.order_id
INNER JOIN products    p  ON oi.product_id  = p.product_id;
```

### JOIN type summary

| JOIN type | Returns |
|---|---|
| `INNER JOIN` | Only rows that match in BOTH tables |
| `LEFT JOIN` | All rows from left + matching rows from right (NULL if no match) |
| `RIGHT JOIN` | All rows from right + matching rows from left (NULL if no match) |
| `FULL JOIN` | All rows from both tables (NULL where no match on either side) |

---

## 7. Subqueries

```sql
-- Subquery in WHERE
-- Find all orders above average order value
SELECT customer_id, order_total
FROM orders
WHERE order_total > (
    SELECT AVG(order_total)
    FROM orders
);

-- Subquery in FROM (derived table)
SELECT city, avg_spend
FROM (
    SELECT
        city,
        AVG(order_total) AS avg_spend
    FROM orders
    GROUP BY city
) AS city_summary
WHERE avg_spend > 300;

-- Subquery in SELECT (scalar subquery)
SELECT
    order_id,
    order_total,
    (SELECT AVG(order_total) FROM orders) AS overall_avg,
    order_total - (SELECT AVG(order_total) FROM orders) AS diff_from_avg
FROM orders;

-- EXISTS — check if related rows exist
SELECT first_name, last_name
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
      AND o.order_total > 1000
);
```

---

## 8. CTEs — Common Table Expressions

```sql
-- Basic CTE
WITH avg_order AS (
    SELECT AVG(order_total) AS avg_val
    FROM orders
)
SELECT order_id, order_total
FROM orders, avg_order
WHERE order_total > avg_val;

-- CTE replacing a messy subquery
-- Without CTE (hard to read):
SELECT city, avg_spend
FROM (SELECT city, AVG(order_total) AS avg_spend FROM orders GROUP BY city) AS s
WHERE avg_spend > 300;

-- With CTE (clean and readable):
WITH city_summary AS (
    SELECT
        city,
        AVG(order_total) AS avg_spend
    FROM orders
    GROUP BY city
)
SELECT city, avg_spend
FROM city_summary
WHERE avg_spend > 300;

-- Multiple CTEs chained together
WITH
monthly_revenue AS (
    SELECT
        MONTH(order_date)  AS month_num,
        SUM(order_total)   AS revenue
    FROM orders
    GROUP BY MONTH(order_date)
),
ranked_months AS (
    SELECT
        month_num,
        revenue,
        RANK() OVER (ORDER BY revenue DESC) AS revenue_rank
    FROM monthly_revenue
)
SELECT *
FROM ranked_months
WHERE revenue_rank <= 3;      -- top 3 revenue months
```

> **When to use CTE vs subquery:**
> Use a CTE when the logic has multiple steps or you need to reuse
> the same result more than once. CTEs are also much easier to debug.

---

## 9. Window Functions

Window functions perform calculations across rows **without** collapsing them like GROUP BY does.

```sql
-- Syntax pattern
function_name() OVER (
    PARTITION BY column    -- optional: restart calculation per group
    ORDER BY column        -- defines row sequence
)
```

### ROW_NUMBER, RANK, DENSE_RANK

```sql
SELECT
    first_name,
    order_total,
    ROW_NUMBER()  OVER (ORDER BY order_total DESC) AS row_num,
    RANK()        OVER (ORDER BY order_total DESC) AS rank_num,
    DENSE_RANK()  OVER (ORDER BY order_total DESC) AS dense_rank
FROM orders;
```

| Value | ROW_NUMBER | RANK | DENSE_RANK |
|---|---|---|---|
| 900 | 1 | 1 | 1 |
| 750 | 2 | 2 | 2 |
| 750 | 3 | 2 | 2 |
| 600 | 4 | 4 | 3 |

> `RANK` skips numbers after a tie (1,2,2,**4**).
> `DENSE_RANK` never skips (1,2,2,**3**).

### PARTITION BY — rank within groups

```sql
-- Rank customers within each city separately
SELECT
    city,
    first_name,
    order_total,
    RANK() OVER (
        PARTITION BY city
        ORDER BY order_total DESC
    ) AS rank_in_city
FROM orders;
```

### Running total

```sql
SELECT
    order_date,
    order_total,
    SUM(order_total) OVER (
        ORDER BY order_date
    ) AS running_total
FROM orders;
```

### LAG & LEAD — compare to previous / next row

```sql
SELECT
    order_date,
    order_total,
    LAG(order_total)  OVER (ORDER BY order_date) AS prev_day_total,
    LEAD(order_total) OVER (ORDER BY order_date) AS next_day_total,
    order_total - LAG(order_total) OVER (ORDER BY order_date) AS day_over_day_change
FROM orders;
```

### NTILE — split into buckets

```sql
-- Divide customers into 4 spend quartiles
SELECT
    first_name,
    order_total,
    NTILE(4) OVER (ORDER BY order_total DESC) AS spend_quartile
FROM orders;
```

---

## 10. CASE WHEN

```sql
-- Simple CASE — like a lookup
SELECT
    order_total,
    CASE
        WHEN order_total >= 1000 THEN 'High value'
        WHEN order_total >= 500  THEN 'Medium value'
        WHEN order_total >= 100  THEN 'Low value'
        ELSE                          'Below threshold'
    END AS order_tier
FROM orders;

-- CASE inside aggregate
SELECT
    COUNT(*)                                           AS total_orders,
    SUM(CASE WHEN order_total >= 500 THEN 1 ELSE 0 END) AS high_value_orders,
    SUM(CASE WHEN status = 'Returned' THEN order_total ELSE 0 END) AS returned_revenue
FROM orders;

-- CASE with PARTITION (% of group)
SELECT
    city,
    order_total,
    CASE
        WHEN order_total > AVG(order_total) OVER (PARTITION BY city)
        THEN 'Above city average'
        ELSE 'Below city average'
    END AS vs_city_avg
FROM orders;
```

---

## 11. NULL Handling

```sql
-- ISNULL (SQL Server) — replace NULL with a default
SELECT
    first_name,
    ISNULL(email, 'no email provided') AS email
FROM customers;

-- COALESCE (works in both MySQL + SQL Server)
-- Returns the first non-NULL value
SELECT
    first_name,
    COALESCE(mobile, phone, email, 'no contact') AS best_contact
FROM customers;

-- NULLIF — returns NULL if two values are equal
-- Useful to avoid division by zero
SELECT
    order_total / NULLIF(quantity, 0) AS price_per_unit
FROM order_items;

-- Filter NULLs
SELECT * FROM customers WHERE email IS NULL;
SELECT * FROM customers WHERE email IS NOT NULL;

-- NULL in aggregates — COUNT(*) includes NULLs, COUNT(col) does not
SELECT
    COUNT(*)        AS all_rows,
    COUNT(email)    AS rows_with_email
FROM customers;
```

---

## 12. String Functions

```sql
-- SQL Server
SELECT
    UPPER(first_name)                    AS name_upper,
    LOWER(email)                         AS email_lower,
    LEN(first_name)                      AS name_length,
    LEFT(first_name, 3)                  AS first_3_chars,
    RIGHT(postcode, 3)                   AS last_3_chars,
    SUBSTRING(phone, 1, 3)               AS area_code,
    TRIM(first_name)                     AS trimmed,
    REPLACE(email, '@gmail.com', '')     AS username,
    CONCAT(first_name, ' ', last_name)   AS full_name,
    CHARINDEX('@', email)                AS at_position
FROM customers;

-- MySQL equivalents
SELECT
    UPPER(first_name),
    LENGTH(first_name)          AS name_length,   -- LEN in SQL Server
    SUBSTR(phone, 1, 3)         AS area_code,     -- SUBSTRING in SQL Server
    LOCATE('@', email)          AS at_position    -- CHARINDEX in SQL Server
FROM customers;
```

---

## 13. Date Functions

```sql
-- SQL Server
SELECT
    GETDATE()                            AS today,
    YEAR(order_date)                     AS order_year,
    MONTH(order_date)                    AS order_month,
    DAY(order_date)                      AS order_day,
    DATEDIFF(DAY, order_date, GETDATE()) AS days_since_order,
    DATEADD(DAY, 30, order_date)         AS due_date,
    FORMAT(order_date, 'yyyy-MM')        AS year_month
FROM orders;

-- MySQL equivalents
SELECT
    NOW()                                         AS today,
    YEAR(order_date)                              AS order_year,
    MONTH(order_date)                             AS order_month,
    DAY(order_date)                               AS order_day,
    DATEDIFF(CURDATE(), order_date)               AS days_since_order,
    DATE_ADD(order_date, INTERVAL 30 DAY)         AS due_date,
    DATE_FORMAT(order_date, '%Y-%m')              AS year_month
FROM orders;

-- Filter by current month (SQL Server)
SELECT * FROM orders
WHERE YEAR(order_date)  = YEAR(GETDATE())
  AND MONTH(order_date) = MONTH(GETDATE());

-- Last 30 days (SQL Server)
SELECT * FROM orders
WHERE order_date >= DATEADD(DAY, -30, GETDATE());

-- Last 30 days (MySQL)
SELECT * FROM orders
WHERE order_date >= DATE_SUB(CURDATE(), INTERVAL 30 DAY);
```

---

## 14. Quick Reference Card

### Clause execution order

SQL clauses do not run in the order you write them.
Understanding this prevents most beginner errors:

```
1. FROM          -- which table(s)?
2. JOIN          -- combine tables
3. WHERE         -- filter rows
4. GROUP BY      -- create groups
5. HAVING        -- filter groups
6. SELECT        -- choose columns
7. DISTINCT      -- remove duplicates
8. ORDER BY      -- sort results
9. LIMIT / TOP   -- restrict row count
```

### Cheat card — one-liners

```sql
-- Distinct count
SELECT COUNT(DISTINCT customer_id) FROM orders;

-- Percentage of total
SELECT city, COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS pct
FROM orders GROUP BY city;

-- Running total
SELECT order_date, SUM(total) OVER (ORDER BY order_date) FROM orders;

-- Top 1 per group
SELECT * FROM (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY city ORDER BY total DESC) AS rn
  FROM orders) t WHERE rn = 1;

-- Previous row comparison
SELECT *, LAG(total) OVER (ORDER BY order_date) AS prev FROM orders;

-- Conditional count
SELECT SUM(CASE WHEN status='Done' THEN 1 ELSE 0 END) AS done_count FROM orders;

-- Replace NULL
SELECT COALESCE(email, 'unknown') FROM customers;

-- Avoid division by zero
SELECT revenue / NULLIF(cost, 0) AS margin FROM products;

-- Current month filter (SQL Server)
SELECT * FROM orders WHERE MONTH(order_date) = MONTH(GETDATE());
```

### Keyword summary table

| Keyword | Use | Example |
|---|---|---|
| `SELECT` | Choose columns | `SELECT name, total` |
| `FROM` | Choose table | `FROM orders` |
| `WHERE` | Filter rows | `WHERE total > 100` |
| `GROUP BY` | Create groups | `GROUP BY city` |
| `HAVING` | Filter groups | `HAVING COUNT(*) > 10` |
| `ORDER BY` | Sort | `ORDER BY total DESC` |
| `LIMIT` / `TOP` | Restrict rows | `LIMIT 10` / `TOP 10` |
| `JOIN` | Combine tables | `INNER JOIN orders ON ...` |
| `WITH` | Define CTE | `WITH cte AS (SELECT ...)` |
| `OVER()` | Window function | `RANK() OVER (ORDER BY total)` |
| `PARTITION BY` | Group in window | `OVER (PARTITION BY city)` |
| `CASE WHEN` | Conditional logic | `CASE WHEN x > 0 THEN 'Y'` |
| `COALESCE` | First non-NULL | `COALESCE(a, b, 'default')` |
| `NULLIF` | Return NULL if equal | `NULLIF(qty, 0)` |

---

## Learning Resources

| Resource | Best for |
|---|---|
| [W3Schools SQL](https://www.w3schools.com/sql/) | Learning syntax with live examples |
| [GeeksforGeeks SQL](https://www.geeksforgeeks.org/sql-tutorial/) | Understanding why things work |
| [SQLHacker](https://www.hackerrank.com/domains/sql) | Interactive practice exercises |
| [LeetCode SQL](https://leetcode.com/problemset/database/) | Interview-style questions |


---

*Devanshi · Self-taught via W3Schools & GeeksforGeeks · Toronto, Canada*
*GitHub: [github.com/Devanshi-20](https://github.com/Devanshi-20)*

