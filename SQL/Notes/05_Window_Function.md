Window Functions

- perform an operation across a set of rows that are somehow related to the current row
- similar to GROUP By aggregate functions, but all rows remain in the output
- process after the entire query except the final ORDER BY statement
- available in PostgreSQL, Oracle, MySQL, but not in SQLite

`OVER` : tells SQL to pass the value over the existing result set

`PARTITION BY` 

- acts like a `GROUP BY` within the window, resetting the calculation for each group
- `ROW_NUMBER` will reset for each partition
- `LAG` will only fetch a row's previous value if its previous row is in the same partition



#### Aggregate Window Function

```sql
SELECT 
	employee_name, 
    salary,
    SUM(salary) OVER() AS grand_total,--show the total salary of all employees on every row
    SUM(salary) OVER(ORDER BY employee_name) AS running_total--calculate the cumulative sum
FROM employees;
```



#### Ranking Window Function

`ROW_NUMBER` assigns a unique, sequential number to each row
 `RANK` assigns a rank and skips numbers if there are ties (1, 2, 2, 4, skip the 3rd)
`DENSE_RANK` assigns a rank and does NOT skip numbers (1, 2, 2, 3)

```sql
SELECT 
	employee_name, 
	department_id, 
    salary, 
    ROW_NUMBER() OVER(PARTITION BY department_id ORDER BY salary DESC) AS row_num,
    RANK() OVER(PARTITION BY department_id ORDER BY salary DESC) AS salary_rank,
    DENSE_RANK() OVER(PARTITION BY department_id ORDER BY salary DESC) AS dense_rank
FROM employees;
```



#### Sliding window

perform calculations relative to the current row
can be used to calculate running totals, sums, averages, etc

##### **keywords:** 

`ROWS BETWEEN <start> AND <finish>` 
`RANGE BETWEEN <start> AND <finish>`  treats duplicates in `OVER`'s `ORDER BY` subclause as a single entity
`n PRECEDING` 
`n FOLLOWING` 
`UNBOUNDED PRECEDING` 
`UNBOUNDED FOLLOWING` 
`CURRENT ROW` 

```sql
SELECT 
	employee_name, 
	department_id,
	salary,
	SUM(salary) OVER(ORDER BY employee_name) AS running_total--the same as 'OVER(ORDER BY employee_name RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)'
FROM employees;
```

```sql
SELECT 
	employee_name,
	department_id, 
	salary, 
	AVG(salary) OVER(ORDER BY salary ROWS BETWEEN 1 PRECEDING AND CURRENT ROW) AS running_avg
FROM employees;
```

##### functions:

`LAG(column, n) OVER (...)` returns column 's value at the row n rows **before** the current row

`LEAD(column, n) OVER (...)` returns column 's value at the row n rows **after** the current row

`FIRST_VALUE(col)` returns the first value in the table or partition

`LAST_VALUE(col)` returns the last value in the table or partition

```sql
SELECT 
	employee_name,
	salary,
	FIRST_VALUE(salary) OVER(
        ORDER BY salary DESC 
        RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS max_salary
FROM employees;
```



#### `NTILE(n)` 

splits data into n approximately equal pages

**Uses:** 
Many APls return data in "pages" to reduce data being sent; 
Separating data into quartiles or thirds (top middle 33%, and bottom thirds) to judge performance

```sql
SELECT 
	employee_name,
	salary, 
	NTILE(3) OVER(ORDER BY salary DESC) AS third
FROM employees;
```









