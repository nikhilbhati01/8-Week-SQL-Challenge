# 🍜 Case Study #1: Danny's Diner

## Solution

***

### 1. What is the total amount each customer spent at the restaurant?

````sql
SELECT	customer_id,  SUM(price) as total_sales
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
USING(product_id)
GROUP BY customer_id
ORDER BY customer_id;
````

#### Answer:
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |


***

### 2. How many days has each customer visited the restaurant?

````sql
SELECT customer_id, COUNT( DISTINCT order_date) as Number_of_visits
FROM dannys_diner.sales
GROUP BY customer_id
ORDER BY customer_id;
````

#### Answer:
| customer_id | Number_of_visits |
| ----------- | -----------      |
| A           | 4                |
| B           | 6                |
| C           | 2                |


***

### 3. What was the first item from the menu purchased by each customer?

````sql
SELECT customer_id, product_name
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
USING(product_id)
WHERE order_date IN (SELECT MIN(order_date) FROM dannys_diner.sales)
GROUP BY customer_id,product_name
ORDER BY customer_id;
````

#### Answer:
| customer_id | product_name | 
| ----------- | ----------- |
| A           | curry        | 
| A           | sushi        | 
| B           | curry        | 
| C           | ramen        |


***

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

````sql
SELECT COUNT(product_id) AS most_purchased, product_name
FROM dannys_diner.menu
INNER JOIN dannys_diner.sales
USING(product_id)
GROUP BY product_name
ORDER BY COUNT(product_id) DESC
LIMIT 1;
````

#### Answer:
| most_purchased | product_name | 
| -----------    | -----------  |
| 8              | ramen        |


***

### 5. Which item was the most popular for each customer?

````sql
WITH sub AS (SELECT customer_id AS customer, product_name, COUNT(product_name) AS order_count, DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(product_name) DESC) AS rank
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
USING(product_id)
GROUP BY customer_id, product_name
)

SELECT customer,  product_name, order_count
FROM sub
WHERE rank=1;
````

#### Answer:
| customer_id | product_name | order_count |
| ----------- | ---------- |------------  |
| A           | ramen        |  3   |
| B           | sushi        |  2   |
| B           | curry        |  2   |
| B           | ramen        |  2   |
| C           | ramen        |  3   |


***

### 6. Which item was purchased first by the customer after they became a member?

````sql

WITh sub AS(SELECT customer_id,order_date, product_name, DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date) as rank
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
USING(product_id)
INNER JOIN dannys_diner.members
USING(customer_id)
WHERE order_date >= join_date
GROUP BY customer_id,product_name,order_date)

SELECT customer_id as Customer, order_date, product_name
FROM sub
WHERE rank=1;

````

#### Answer:

| Customer    | order_date | product_name |
| ----------- | ---------- |----------    |
| A           | 2021-01-07 | curry        |
| B           | 2021-01-11 | sushi        |


***

### 7. Which item was purchased just before the customer became a member?

````sql
WITh sub AS(SELECT customer_id,product_id, order_date,  DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date DESC) as rank
FROM dannys_diner.sales
INNER JOIN dannys_diner.members
USING(customer_id)
WHERE order_date < join_date
)

SELECT customer_id as Customer, order_date, product_name
FROM sub
INNER JOIN dannys_diner.menu
USING(product_id)
WHERE rank=1
ORDER BY customer_id;
````

#### Answer:
| Customer    | order_date | product_name  |
| ----------- | ---------- |----------     |
| A           | 2021-01-01 |  sushi        |
| A           | 2021-01-01 |  curry        |
| B           | 2021-01-04 |  sushi        |


***

### 8. What is the total items and amount spent for each member before they became a member?

````sql
SELECT customer_id, COUNT(DISTINCT product_name) As num_items, SUM(price) as money_spent
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
USING(product_id)
INNER JOIN dannys_diner.members
USING(customer_id)
WHERE order_date < join_date
GROUP BY customer_id
ORDER BY customer_id;

````

#### Answer:
| customer_id | num_items | money_spent |
| ----------- | --------- |----------   |
| A           | 2 |  25                 |
| B           | 2 |  40                 |

***

### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points would each customer have?

````sql
WITH sub AS 
(
SELECT customer_id, 
CASE WHEN product_id=1 THEN price*20
else price*10 END AS multiplier
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
USING(product_id)

)

SELECT customer_id, SUM(multiplier) as points
FROM sub
GROUP BY customer_id
ORDER BY customer_id;
````

#### Answer:
| customer_id | points | 
| ----------- | ---------- |
| A           | 860 |
| B           | 940 |
| C           | 360 |


***

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi — how many points do customer A and B have at the end of January?

````sql
SELECT c.customer_id,  
SUM(CASE WHEN c.join_date <= a.order_date and a.order_date < 7+ (c.join_date)  THEN 2*10*b.price
	 ELSE 1*10*b.price
	 END) points
FROM dannys_diner.sales AS a 
LEFT JOIN dannys_diner.menu AS b
ON a.product_id = b.product_id 
RIGHT JOIN dannys_diner.members AS c
ON a.customer_id = c.customer_id
WHERE a.order_date < '2021-02-01' GROUP BY c.customer_id ORDER BY c.customer_id; 
````


#### Answer:
| customer_id | points     | 
| ----------- | ---------- |
| A           | 1370       |
| B           | 820        |


***
