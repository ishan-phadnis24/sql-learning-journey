# 🛒 Flipkart SQL Case Study

## 📌 Problem Statement

Flipkart, one of India's leading e-commerce platforms, wants to strengthen its competitive position by improving customer retention, optimizing logistics, and increasing sales efficiency.

The dataset contains information about:

* Customers
* Products
* Orders
* Order Items

**Time Period:** January 2023 – September 2025

---

## 📂 Dataset

**Google Drive Dataset:**
https://drive.google.com/drive/folders/1IJ8rjUVSS98T5W5npt611N0pk-RZs6NK

---

# 🪟 Window Functions

## Question 1

**Display the average order amount of all customers in a new column for each order in the orders table.**

```sql
SELECT
    order_id,
    customer_id,
    amount,
    order_date,
    AVG(amount) OVER () AS avg_order_amount
FROM `flipkart.orders`;
```

---

## Question 2

**For each order in the orders table, display the order amount along with the total amount spent by the customer across all their orders in a new column.**

```sql
SELECT
    order_id,
    customer_id,
    amount,
    order_date,
    SUM(amount) OVER (PARTITION BY customer_id) AS total_cust_spent
FROM `flipkart.orders`;
```

---

## Question 3

**Display detailed information on orders whose amount is greater than the company-wide average order amount.**

```sql
WITH amount_stats AS (
    SELECT
        order_id,
        customer_id,
        amount,
        order_date,
        AVG(amount) OVER () AS avg_amt_comp_wise
    FROM `flipkart.orders`
)

SELECT *
FROM amount_stats
WHERE amount > avg_amt_comp_wise;
```

---

## Question 4

**Show details of orders whose amounts exceed the average order amount of their respective customers.**

```sql
WITH avg_amt_cus AS (
    SELECT
        order_id,
        customer_id,
        amount,
        order_date,
        AVG(amount) OVER (PARTITION BY customer_id) AS avg_amount
    FROM `flipkart.orders`
)

SELECT *
FROM avg_amt_cus
WHERE amount > avg_amount;
```

---

# 🔢 NTILE()

## Question 5

**Divide all the orders into 4 different buckets (quartiles) based on their order amounts.**

* 1st Quartile → Lowest order amounts
* 4th Quartile → Highest order amounts

```sql
SELECT
    order_id,
    customer_id,
    amount,
    NTILE(4) OVER (ORDER BY amount) AS amount_group
FROM `flipkart.orders`
WHERE amount IS NOT NULL;
```

---

# ⏮️ LAG() & LEAD()

## Question 6

**For each order, provide its details and amount. Additionally, identify the amount of the previous order and the next order in the company based on `order_id`.**

```sql
SELECT
    order_id,
    customer_id,
    amount,
    LAG(amount, 1) OVER (ORDER BY order_id) AS previous_order_amt,
    LEAD(amount) OVER (ORDER BY order_id) AS next_order_amt
FROM `flipkart.orders`;
```

---

# ⭐ FIRST_VALUE() & LAST_VALUE()

## Question 7

**Compare each customer's order amount with the first order amount (based on order date) in their respective order history.**

```sql
SELECT
    customer_id,
    order_id,
    order_date,
    amount,
    FIRST_VALUE(amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) AS first_order_amount,

    FIRST_VALUE(amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date DESC
    ) AS last_order_amount,

    LAST_VALUE(amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING
        AND UNBOUNDED FOLLOWING
    ) AS last_order_amount
FROM `flipkart.orders`;
```

---

# 🎯 NTH_VALUE()

## Question 8

**Calculate the 3rd highest order amount for each customer.**

```sql
SELECT
    customer_id,
    order_id,
    amount,
    NTH_VALUE(amount, 3) OVER (
        PARTITION BY customer_id
        ORDER BY amount DESC
        ROWS BETWEEN UNBOUNDED PRECEDING
        AND UNBOUNDED FOLLOWING
    ) AS third_highest_amount
FROM `flipkart.orders`;
```

---

## 📚 SQL Concepts Covered

* Window Functions
* `OVER()`
* `PARTITION BY`
* `ORDER BY`
* `AVG()`
* `SUM()`
* `NTILE()`
* `LAG()`
* `LEAD()`
* `FIRST_VALUE()`
* `LAST_VALUE()`
* `NTH_VALUE()`
* Common Table Expressions (CTEs)

---

**⭐ If you found this repository helpful, feel free to star it!**
