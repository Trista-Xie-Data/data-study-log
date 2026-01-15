#### Relational Databases

##### Table naming

Lowercase and use underscores
Plural

##### Field naming

Lowercase and use underscores
Singular
Different to the table and other fields

##### Schema

Shows a database's design, such as what tables are included, any relationships between its tables, and what data type each field can hold. 

#### Querying

##### Select all of the fields in the table

```sql
SELECT * 
FROM table;
```

##### Aliasing

```sql
SELECT name AS rename
FROM table;
```

##### Unique values

```sql
SELECT DISTINCT column_1, column_2
FROM table;
```

[^return unique combinations of the two fields]: 

##### Views

A view is a saved SQL query that acts like a virtual table. 
Store the query instead of data.
Ensuring the results are always up-to-date with the latest database changes.

```sql
CREATE VIEW name AS
SELECT column
FROM table;
```

