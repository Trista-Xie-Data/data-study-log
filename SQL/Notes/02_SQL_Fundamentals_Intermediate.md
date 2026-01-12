## Order of execution

| Written code | Execution oder |
| ------------ | -------------- |
| SELECT       | FROM / JOIN    |
| FROM / JOIN  | WHERE          |
| WHERE        | GROUP BY       |
| GROUP BY     | HAVING         |
| HAVING       | SELECT         |
| ORDER BY     | ORDER BY       |
| LIMIT        | LIMIT          |

------

## Filtering

**`WHERE`** 
can't filter aggregate function with WHERE clause

### Comparison operators

**`>`**    Greater than or after
**`<` **   Less than or before
**`=`**    Equal to
**`>=`**  Greater than or equal to
**`<=`**  Less than or equal to
**`<>`**  Not equal to

**`OR`** only need to satisfy at least one condition
**`AND`** satisfy all criteria in the filter

```sql
SELECT field
FROM table
WHERE (num = 1 OR num = 2)
	AND (str = 'A' OR str = 'B');
```

**`BETWEEN`** -filtering values within a specified range, the results contain the beginning and end values.

```sql
SELECT field
FROM table
WHERE num BETWEEN 1 AND 100
	AND str = 'A';
```

### Filtering text

**`LIKE`** 
**`NOT LIKE`** 

**`%`** match zero, one, or many characters
**`_`** match a single character

```sql
SELECT field
FROM table
WHERE str LIKE '__t%';
```

**`IN`** specify multiple values

```sql
SELECT column
FROM table
WHERE str IN ('A', 'B');
```

### Null values

`COUNT(field_name)`includes only non-missing values
`COUNT(*)`includes missing values

**`IS NULL`** 
**`IS NOT NULL`** 

------

## Aggregation functions

perform a calculation on several values and return a single value.

`COUNT()` -counts the number of records with a value in a field.

```sql
SELECT COUNT(DISTINCT field) AS new_name
FROM table;
```

[^counting the number of unique values in a field]: 

`MIN()` ,  `MAX()` -return the record coming first or last in the alphabet when it comes to non-numerical data.` 

`AVG()` , `SUM()` -numerical fields only

`ROUND(num_to_round, decimal_places)` zero is the default when the second argument is not given. 

*Use an alias for clarity.* 

------

## Arithmetic

`+` , `-` , `*` , `/` 

```sql
SELECT(3/2)
--result: 1
SELECT(3.0/2.0)
--result: 1.5
```

[^SQL assumes that we want to get an integer back if we divide an integer by an integer, we can add decimal places to our numbers if we want more precision.]: 

------

## Sorting  and Grouping

**`ORDER BY`** 

```sql
SELECT field_1, field_2
FROM table
ORDER BY field_1 ASC, field_2 DESC;
```

**`GROUP BY`** 
commonly used with aggregate functions to provide summary statistics. This is because the aggregate function will reduce the non-grouped field to one record only, which will need to correspond to one group.

### Filtering grouped data

**`HAVING`** 

```sql
SELECT field_1, COUNT(field_2)
FROM table
GROUP BY field_1
HAVING COUNT(field_2) > 10;
```

------

## Common SQL errors

Misspelling
Incorrect capitalization
Incorrect or missing punctuation, especially commas
Field name with spaces *[A SQL mistake is including spaces in a field name. To query that table, we'll need to enclose the field name in double-quotes(PostgreSQL) or single-quote(MySQL) to indicate that, despite being two words, the name refers to just one field.]* 

