Joining data enable us to draw information from separate tables together into a single, meaningful set of results.

Use these two tables to demonstrate different JOIN types: 

table_1(left table): 

|  id  |  name   |
| :--: | :-----: |
|  1   |  Alice  |
|  2   |   Bob   |
|  3   | Charlie |
|  4   | Daniel  |

table_2(right table): 

|  id  | product  |
| :--: | :------: |
|  1   |  Laptop  |
|  4   |  Mouse   |
|  6   | Keyboard |
|  7   | Monitor  |



------

#### `INNER JOIN` 

looks for records in both tables with the same values in the key field

```sql
SELECT t1.id, name, product
FROM table_1 AS t1
INNER JOIN table_2 AS t2
USING(id);
```

return:

|  id  |  name  | product |
| :--: | :----: | :-----: |
|  1   | Alice  | Laptop  |
|  4   | Daniel |  Mouse  |



------

#### Outer Joins

##### `LEFT JOIN` 

return all records in the left_table, and those records in the right_table that match on the joining field provided.

```sql
SELECT t1.id, name, product
FROM table_1 AS t1
LEFT JOIN table_2 AS t2
USING(id);
```

return:

|  id  |  name   | product |
| :--: | :-----: | :-----: |
|  1   |  Alice  | Laptop  |
|  2   |   Bob   |  null   |
|  3   | Charlie |  null   |
|  4   | Daniel  |  Mouse  |

*Note. `LEFT JOIN` can also be written as `LEFT OUTER JOIN`*  



##### `RIGHT JOIN` 

all records are retained from right_table, even when id doesn't find a corresponding match in left_table.

a RIGHT JOIN can always be re-written as a LEFT JOIN.

```sql
SELECT t1.id, name, product
FROM table_1 AS t1
RIGHT JOIN table_2 AS t2
USING(id);
```

return:

|  id  |  name  | product  |
| :--: | :----: | :------: |
|  1   | Alice  |  Laptop  |
|  4   | Daniel |  Mouse   |
|  6   |  null  | Keyboard |
|  7   |  null  | Monitor  |

*Note. `RIGHT JOIN` can also be written as `RIGHT OUTER JOIN`*  



##### `FULL JOIN`

retain all ids, return missing values in the form of nulls for fields corresponding to records where id did not find a match.

```sql
SELECT t1.id AS L_id, 
	t2.id AS R_id, 
	name, 
	product
FROM table_1 AS t1
FULL JOIN table_2 AS t2
USING(id);
```

return:

| L_id | R_id |  name   | product  |
| :--: | :--: | :-----: | :------: |
|  1   |  1   |  Alice  |  Laptop  |
|  2   | null |   Bob   |   null   |
|  3   | null | Charlie |   null   |
|  4   |  4   | Daniel  |  Mouse   |
| null |  6   |  null   | Keyboard |
| null |  7   |  null   | Monitor  |

*Note. The keyword `FULL OUTER J0IN` can also be used.*



The two code snippets below produce identical output. When using FULL JOIN with the USING clause, the join column is automatically coalesced, performing the same logic as COALESCE, merging values from both tables into a single column.

```sql
SELECT *
FROM table_1 AS t1
FULL JOIN table_2 AS t2
USING(id);
```

```sql
SELECT COALESCE(t1.id, 	t2.id) AS id, 
	name, 
	product
FROM table_1 AS t1
FULL JOIN table_2 AS t2
USING(id);
```

return:

|  id  |  name   | product  |
| :--: | :-----: | :------: |
|  1   |  Alice  |  Laptop  |
|  2   |   Bob   |   null   |
|  3   | Charlie |   null   |
|  4   | Daniel  |  Mouse   |
|  6   |  null   | Keyboard |
|  7   |  null   | Monitor  |



------

#### `CROSS JOIN`

creates all possible combinations of two tables.

```sql
SELECT name, product
FROM table_1
CROSS J0IN table_2
LIMIT 8;
```

|  name   | product  |
| :-----: | :------: |
|  Alice  |  Laptop  |
|   Bob   |  Mouse   |
| Charlie | Keyboard |
| Daniel  | Monitor  |
|  Alice  |  Laptop  |
|   Bob   |  Mouse   |
| Charlie | Keyboard |
| Daniel  | Monitor  |



------

#### Self Joins

compare values from part of a table to other values from within the same table

```sql
SELECT t1.name AS p1
	t2.name AS p2
	t1.id
FROM table_1 AS t1
INNER JOIN table_1 AS t2
ON t1.id = t2.id
	AND t1.name <> t2.name
LIMTI 8;
```

return: 

| p1      | p2      | id   |
| ------- | ------- | ---- |
| Alice   | Bob     | 1    |
| Alice   | Charlie | 1    |
| Alice   | Daniel  | 1    |
| Bob     | Alice   | 2    |
| Bob     | Charlie | 2    |
| Bob     | Daniel  | 2    |
| Charlie | Alice   | 3    |
| Charlie | Bob     | 3    |



------

#### Joins on joins

```sql
SELECT *
FROM left table
INNER J0IN right table
ON left_table.id = right_table.id
INNER J0IN another_table
ON left_table.id = another table.id;
```

*Note. Depending on the use case, left_table or right_table can be used in the 0N clause* 



------

#### Joining on multiple keys

```sql
SELECT *
FROM left_table
INNER JOIN right_table
0N left_table.id = right_table.id
AND left_table.date = right table.date;
```



------

#### Set theory operations

For all set operations, the number of selected columns and their respective data types must be identical. 

##### `UNION` 

takes two tables as input, return all records from both tables
the result will only use field names (or aliases, if used) of the first SELECT statement in the query

```sql
SELECT *
FROM left_table
UNION
SELECT *
FROM right_table;
```

##### `UNION ALL`

if two records are identical, UNION only returns them once, while UNION ALL will include duplicate records

##### `INTERSECT` 

returns only the records that exist in both tables

```sql
SELECT *
FROM left_table
INTERSECT
SELECT *
FROM right_table;
```

##### `EXCEPT` 

retains only records from the left table that are not present in the right table.



------

#### Semi join

choose records in the first table where a condition is met in the second table

```sql
SELECT *
FROM table_1 AS t1
WHERE id IN (
	SELECT id
	FROM table_2 AS t2
	WHERE t1.id = t2.id);
```



------

#### Anti join

chooses records in the first table where the specify column does NOT find a match in the second table.

```sql
SELECT *
FROM table_1 AS t1
WHERE id NOT IN (
	SELECT id
	FROM table_2 AS t2
	WHERE t1.id = t2.id);
```

