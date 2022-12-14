# 🍜 Case Study #2: Pizza Runner

## Solution

***


### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql
SELECT CASE 
WHEN registration_date BETWEEN '2021-01-01' AND '2021-01-07' THEN '2021-01-01'
WHEN registration_date BETWEEN '2021-01-08' AND '2021-01-14' THEN '2021-01-08'
WHEN registration_date BETWEEN '2021-01-15' AND '2021-01-21' THEN '2021-01-15'
END AS registration_week,
COUNT(runner_id) as runner_signups
FROM pizza_runner.runners
GROUP BY registration_week
ORDER BY registration_week;
````

#### Answer:
| registration_week | runner_signups |
| ----------------  |  ------------  |           
|     2021-01-01    |       2        |
|     2021-01-08    |       1        |
|     2021-01-15    |       1        |
