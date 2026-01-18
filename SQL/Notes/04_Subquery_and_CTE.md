#### Simple subqueries

can be placed in any part of query (including `SELECT`, `FROM`, `WHERE`, `GROUP BY`)
also can return a variety of information

**usage scenario:** 
comparing groups to summarized values, reshaping data, 
combining data that cannot be joined

```sql
SELECT coL
FROM (SELECT coL
	FROM table) AS subquery;
```

**subqueries in the WHERE:** can only return a single column

**subqueries in the FROM:** restructure and transform data, prefilter data, calculate aggregates of aggregate information. Don't forget to alias.

**subqueries in the SELECT:** used in mathematical calculations and return a single value



#### Correlated subqueries

- Uses values from the outer query to generate a result
- Re-run for every row generated in the final data set
- Used for advanced joining, filtering, and evaluating data

```sql
SELECT e1.employee_name, e1.salary, e1.department_id
FROM employees AS e1 
WHERE salary > (
    SELECT AVG(salary)
    FROM employees AS e2
    -- correlation point：inner table e2 references outer table e1
    WHERE e2.department_id = e1.department_id 
);
```



#### Nested subqueries

- Subquery inside another subquery
- Perform multiple layers of transformation

```sql
SELECT employee_name
FROM employees
WHERE department_id IN (
    -- middle layer
    -- use location IDs ti find corresponding department IDs
    SELECT department_id
    FROM departments
    WHERE location_id IN (
        -- innermost layer
        -- execute frst to find location IDs in the JPN
        SELECT location_id
        FROM locations
        WHERE country_id = 'JPN'
    )
);
```

##### Multi-level mixed subquery (nested + correlated)

```sql
SELECT e.employee_name
FROM employees e
WHERE e.manager_id IN (
    -- identify managers who meet the salary criteria
    SELECT m.employee_id
    FROM employees m
    WHERE m.salary > (
        -- correlated subquery
        -- m.department_id references the table 'm'
        SELECT AVG(salary)
        FROM employees e2
        WHERE e2.department_id = m.department_id
    )
);
```



#### CASE 

`CASE WHEN x = 1 THEN 'a'
	  WHEN x = 2 THEN 'b'
	  ELSE 'c' 
	  END AS new_column` 

**Categorizing, Filtering Aggregating with CASE statements:** CASE statements are like any other column in the query, so it can be placed inside the WHERE clause or aggregate function. This allows for complex, conditional categorizing, filtering, aggregating by check the direct output of the CASE logic (e.g., keeping only rows where the CASE statement IS NOT NULL).

```sql
SELECT 
	col1, 
	COUNT(CASE WHEN condition1 THEN 'a') AS a
	COUNT(CASE WHEN condition2 THEN 'b') AS b
FROM table
GROUP BY col1;
```



#### CTE (Common Table Expression)

Table declared before the main query
Named and referenced later in FROM statement
Run only once

```sql
WITH name1 AS (
	SELECT col
	FROM table
	WHERE condition1)
	name2 AS (
    SELECT col
    FROM table
    WHERE condition2)
```

CTE can reference other CTEs and even itself



#### Difference between JOIN, subquery and CTE

**JOIN** : directly combine two or more tables, and on their own, are mostly limited to simple combinations and aggregations of tables already present in the database

**Correlated subquery** : can combine information between a subquery and a table, or another subquery, so it's great for matching data from different columns in one or more tables
take a long time to process

**Nested subquery** : require multi-step transformations
breaking down the steps of the query process allows for better accuracy and reproducibility

**CTE** : organize the subqueries sequentially by declaring them at the beginning of the query





