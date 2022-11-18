# 🍜 Case Study #2: Pizza Runner

## Solution

***


### 1. How many pizzas were ordered?

````sql
SELECT COUNT(*) AS NO_OF_PIZZAS
FROM pizza_runner.customer_orders;
````

#### Answer:
| no_of_pizzas |
| -----------  |
|     14       |


### 2. How many unique customer orders were made?

````sql
SELECT COUNT( DISTINCT order_id ) as Unique_orders
FROM pizza_runner.customer_orders;
````

#### Answer:
| unique_orders |
| -----------   |
|     10        |


### 3. How many successful orders were delivered by each runner?

````sql
SELECT runner_id, COUNT(order_id) as successful_orders
FROM pizza_runner.runner_orders
WHERE duration != 'null'
GROUP BY runner_id
ORDER BY runner_id;
````

#### Answer:
| runner_id   | successful_orders |
| ----------- | -----------       |
| 1           | 4                 |
| 2           | 3                 |
| 3           | 1                 |


### 4. How many of each type of pizza was delivered?

````sql
SELECT pizza_name, COUNT(c.pizza_id) as delivered_pizza_count
FROM pizza_runner.customer_orders as c
LEFT JOIN pizza_runner.runner_orders as r
ON r.order_id = c.order_id
LEFT JOIN pizza_runner.pizza_names as p
ON p.pizza_id = c.pizza_id
WHERE distance != 'null'
GROUP BY p.pizza_name;
````

#### Answer:
| pizza_name   | delivered_pizza_count |
| -----------  | --------------------  |
| Meatlovers   | 9                     |
| Vegetarian   | 3                     |


### 5. How many Vegetarian and Meatlovers were ordered by each customer?

````sql
SELECT customer_id, pizza_name , COUNT(c.pizza_id) as order_count
FROM pizza_runner.customer_orders as c
LEFT JOIN pizza_runner.pizza_names as p
ON c.pizza_id = p.pizza_id
GROUP BY customer_id,pizza_name
ORDER BY customer_id;
````

#### Answer:
| customer_id   | Pizza_name      | order_count |
| -----------   | -------------   | ----------- |
|   101         |   Meatlovers    |    2        |
|   101         |   Vegetarian    |    1        |
|   102         |   Meatlovers    |    2        |
|   102         |   Vegetarian    |    1        |
|   103         |   Meatlovers    |    3        |
|   103         |   Vegetarian    |    1        |
|   104         |   Meatlovers    |    3        |
|   105         |   Vegetarian    |    1        |


### 6. How many successful orders were delivered by each runner?

````sql
WITH sub as (
SELECT c.order_id, COUNT(pizza_id) as no_of_pizza
FROM pizza_runner.customer_orders as c 
LEFT JOIN pizza_runner.runner_orders as r
ON r.order_id = c.order_id
WHERE distance != 'null'
GROUP BY c.order_id)

SELECT order_id, MAX(no_of_pizza) as max_no_of_pizzas
FROM sub
GROUP BY order_id
ORDER BY max_no_of_pizzas DESC
LIMIT 1;
````

#### Answer:
| order_id    | max_no_of_pizzas |
| ----------- | ---------------  |
| 4           | 3                |


### 7. How many pizzas were delivered that had both exclusions and extras?

````sql
SELECT COUNT(pizza_id) as pizza_count_w_exclusions_extras
FROM pizza_runner.customer_orders as c
LEFT JOIN pizza_runner.runner_orders as r
ON c.order_id = r.order_id
WHERE distance != 'null' AND exclusions NOT IN ('','null') AND extras NOT IN ('','null','NaN');
````

#### Answer:
| pizza_count_w_exclusions_extras |
| ------------------------------  |
|               1                 |


### 8. What was the total volume of pizzas ordered for each hour of the day?

````sql
SELECT EXTRACT (HOUR FROM order_time) AS hour_of_day, COUNT(pizza_id) as pizza_count
FROM pizza_runner.customer_orders
GROUP BY hour_of_day
ORDER BY hour_of_day;
````

#### Answer:
| hour_of_day | pizza_count |
| ----------- | ----------  |
| 11          | 1           |
| 13          | 3           |
| 18          | 3           |
| 19          | 1           |
| 21          | 3           |
| 23          | 3           |








***
