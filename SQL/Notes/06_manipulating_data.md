#### Determining data types from existing tables

```sql
SELECT
	column_name,
	data_type
FROM INFORMATION_SCHEMA.COLUMNS
WHERE column_name IN('col1', 'col2')
	AND table_name ='table';
```



#### Common data types in PostgreSQL

- **text:** `CHAR(n)` (fixed), `VARCHAR(n)` (variable), `TEXT` 

- **numeric:** `INT(p)`, `DECIMAL(p,s)` 

- **data / time:** `DATE`, `TIME`, 
  `TIMESTAMP`(contains the date and time values; no time zone by default), 
  `INTERVAL` (store date and time data as a period of time)

  
  
  *p: precision* 
  
  *s: scale* 
  
- `ARRAY` 

  ```sql
  --create table and insert
  CREATE TABLE table1(
  	col1 VARCHAR(255),
  	col2 INTEGER);
  
  INSERT INTO table1 (col1, col2) VALUES('AAA', 5);
  
  --create table with array columns
  CREATE TABLE table2(
  	col1 INTEGER, 
  	col2 text[][], 
  	col3 INTEGER[]);
  
  INSERT INTO table2 VALUES (
      1, 
      '{{"Alice", "work_address"},{"Bob", "home_address"}}', 
      '{9,6,10,7}'
  );
  
  --access arrays
  SELECT 
  	col2[1][1] AS name, 
  	col2[1][2] AS address,
  	col3[1] AS num
  FROM table2
  LIMIT 1;
  ```
  
    return: 
  
  | name  | address      | num  |
  | ----- | ------------ | ---- |
  | Alice | work_address | 9    |
  
    **search an array:** 
  
    `WHERE 'text_xxx' = ANY(col_name)` 
  
    `WHERE col_name @> ARRAY['text_xxx']` 
  
  
#### Date and time functions and operators

`CURRENT_DATE()` , `CURRENT_TIMESTAMP()` , `NOW()` 

`AGE()` calculate the difference between two timestamps

##### Adding and subtracting

```sql
SELECT date '2026-01-20' - date '2026-01-19'; 
--result: 1

SELECT date '2026-01-20' + INTEGER '3';
--result: 2026-01-23

SELECT date '2026-01-21 00:00:00' - date '2026-01-19 12:00:00';

SELECT AGE(timestamp '2026-01-21 00:00:00', timestamp '2026-01-19 12:00:00');
--result: 1 day 12:00:00

SELECT timestamp '2026-01-01' + 7 * INTERVAL '3 days';
--result: 2026-01-22 00:00:00
```

##### Extracting and transforming

`EXTRACT()` , `DATE_PART()` 

extract year, month, quarter or day of week from timestamp, time, or interval data type

```sql
SELECT EXTRACT(quarter FROM TIMESTAMP'2026-01-19 12:15:43');

SELECT DATE_PART('quarter', TIMESTAMP'2026-01-19 12:15:43');
--result: 1
```

 `DATE_TRUNC()` truncate timestamp or interval data types to return a timestamp or interval at a specified precision. 

```sql
SELECT DATE_TRUNC('month', TIMESTAMP'2026-08-19 12:15:43');
--result: 2026-08-01 00:00:00
```



#### Parsing and manipulating text

##### concatenating string

```sql
SELECT
	first_name,
	last_name,
	first_name || '' || last_name AS full_name
FROM customer;

--PostgreSQL built-in function
SELECT CONCAT(first_name,' ',last_name) AS full_name
FROM customer;
```

##### changing the case

`UPPER()` , `LOWER()` , 

`INITCAP()` -title case

##### replacing characters

```sql
SELECT REPLACE(col, 'before', 'after') AS col
FROM table;
```

##### determining the length

`CHAR_LENGTH(str)` , `LENGTH(str)`

##### finding the position of a character

`POSITION('char' IN str)` , `STRPOS(str, 'char')` 

##### parsing

`LEFT(str, number_of_characters)` , `RIGHT(str, n)` 

`SUBSTRING(str, start, length)` , `SUBSTR(str, start, length)` -extract a substring from text data

`SUBSTRING(str FROM start FOR ending)` -combine with other functions

```sql
SELECT 
	SUBSTRING(str 
              FROM POSITION('char' IN str)+1 
              FOR CHAR_LENGTH(str))
FROM table;
```

##### removing characters

`TRIM(position 'char' FROM str)` 

*position(leading | trailing | both)* specifies whether you want to remove characters from the beginning or end of a string or both; the default value is both.

the second parameter's default value is space.

`LTRIM(str, 'char')` , `RTRIM(str, 'char')`

```sql
SELECT TRIM('  string  ');
--result: string

SELECT LTRIM('$$string$$', '$');
--result: string$$

SELECT RTRIM('$$string$$', '$');
--result: $$string
```

##### Padding

`LPAD(str, length, 'char')` 

`RPAD(str, length, 'char')` 

```sql
SELECT LPAD('string', 10, '#');
--return: ####string
```



#### Full-text search and PostgreSQL Extensions

##### Full Text search

```sql
SELECT col1, col2
FROM table
WHERE to_tsvector(col1) @@ to_tsquery('char');
```

##### Extending PostgreSQL

**Commonly used extensions** 

PostGIS, 。PostPic, fuzzystrmatch, pg_trgm

```sql
--determine the list of extensions that are available to be installed and enabled for use
SELECT name
FROM pg_available_extensions;

--determine which extensions have already been enables in the database and are currently available for use
SELECT extname
FROM pg_extension;
```

**user-defined data types** 

```sql
CREATE TYPE dayofweek AS ENUM (
	'Monday', 
	'Tuesday', 
	'Wednesday', 
	'Thursday', 
	'Friday', 
    'Saturday', 
    'Sunday'
);

--query the system table pg_type to get information about all data types available in the database both user-defined and built-in
SELECT typname, typcategory
FROM pg_type
WHERE typname = 'dayofweek';

SELECT column_name, data_type, udt_name
FROM INFORMATION_SCHEMA.COLUMNS
WHERE table_name = 'table';
```

**user-defined functions** 

bundling several SQL queries and statements together into a single package by using the CREATE FUNCTION command. 

```sql
CREATE FUNCTION squared(i integer) RETURNS integer AS $$
	BEGIN
		RETURN i * i; 
	END;
$$ LANGUAGE plpgsql;
```

#### Improving full text search with extensions

```sql
--enable the fuzzystrmatch extension
CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;

--calculates the levenshtein distance between two strings which is the number of edits required for the strings to be a perfect match
SELECT levenshtein('GUMBO', 'GAMBOL');
--result: 2

CREATE EXTENSION IF NOT EXISTS pg_trgm;
--determine how similar these two strings are
SELECT similarity('GUMBO', 'GAMBOL');
--result: 0.1818
```









