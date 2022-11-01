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
FROM pizza_runner.customer_orders ;
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
| ----------- | -----------      |
| 1           | 4                |
| 2           | 3                |
| 3           | 1                |


















***
