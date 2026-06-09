# SQL Notes for Beginners

A complete beginner-friendly SQL reference from 0 to 1.

---

## Table of Contents

1. [DDL vs DML](#ddl-vs-dml)
2. [Core Statements](#core-statements)
3. [Creating and Managing Tables](#creating-and-managing-tables)
4. [Primary Key vs Foreign Key](#primary-key-vs-foreign-key)
5. [String Patterns and Ranges](#string-patterns-and-ranges)
6. [Sorting and Grouping](#sorting-and-grouping)
7. [Built-in Functions](#built-in-functions)

---

## DDL vs DML

SQL statements are split into two main categories.

**DDL (Data Definition Language)** defines and manages the structure of the database.

**DML (Data Manipulation Language)** manages the data inside the tables.

| Category | Commands | What it does |
|----------|----------|--------------|
| DDL | CREATE, ALTER, DROP, TRUNCATE | Manages table structure |
| DML | SELECT, INSERT, UPDATE, DELETE | Manages data inside tables |

---

## Core Statements

### SELECT

Retrieves data from a table.

```sql
-- select all columns
SELECT * FROM users;

-- select specific columns
SELECT name, email FROM users;
```

### COUNT

Returns the number of rows matching a condition.

```sql
-- count all rows
SELECT COUNT(*) FROM users;
```

### DISTINCT

Removes duplicate values from results.

```sql
-- get unique values only
SELECT DISTINCT department FROM employees;
```

You can combine COUNT and DISTINCT together.

```sql
-- count how many unique first names exist
SELECT COUNT(DISTINCT firstname) FROM instructor;
```

### LIMIT

Restricts how many rows are returned.

```sql
-- return only the first 5 rows
SELECT * FROM users LIMIT 5;

-- skip 10 rows then return the next 5
SELECT * FROM users LIMIT 5 OFFSET 10;
```

### INSERT

Adds new rows into a table.

```sql
INSERT INTO users (id, name, email, age)
VALUES (1, 'John', 'john@email.com', 25);
```

### UPDATE

Modifies existing rows. Always use a WHERE clause, otherwise every row gets updated.

```sql
-- correct
UPDATE users SET age = 25 WHERE id = 3;

-- dangerous: updates every single row
UPDATE users SET age = 25;
```

### DELETE

Removes rows from a table. Always use a WHERE clause for the same reason.

```sql
-- delete a specific row
DELETE FROM users WHERE id = 3;

-- delete all rows (table still exists)
DELETE FROM users;
```

### WHERE

Filters rows based on a condition.

```sql
SELECT * FROM users WHERE age = 25;
SELECT * FROM users WHERE age > 18;
SELECT * FROM users WHERE age BETWEEN 18 AND 30;
```

---

## Creating and Managing Tables

### CREATE TABLE

Defines a new table with columns and data types.

```sql
CREATE TABLE BookShop (
    BOOK_ID INT PRIMARY KEY,
    TITLE VARCHAR(100) NOT NULL,
    AUTHOR VARCHAR(100),
    PRICE DECIMAL(5, 2) CHECK (PRICE >= 0),
    PUBLISHED_DATE DATE
);
```

Common data types:

| Type | Description |
|------|-------------|
| `INT` | Whole numbers |
| `VARCHAR(n)` | Text up to n characters |
| `TEXT` | Unlimited text |
| `DECIMAL(p, s)` | Exact decimal (good for money) |
| `FLOAT` | Approximate decimal |
| `BOOLEAN` | True or false |
| `DATE` | Date only (YYYY-MM-DD) |
| `DATETIME` | Date and time |

`DECIMAL(5, 2)` means 5 digits total with 2 after the decimal point, so the max value is `999.99`.

Common constraints:

```sql
PRIMARY KEY       -- unique identifier for each row
NOT NULL          -- value is required
UNIQUE            -- no duplicates allowed
DEFAULT 'value'   -- fallback value if none is provided
CHECK (condition) -- enforces a rule on the value
```

### ALTER

Modifies an existing table structure.

```sql
-- add a new column
ALTER TABLE users ADD phone VARCHAR(20);

-- remove a column
ALTER TABLE users DROP COLUMN phone;

-- change a column's data type
ALTER TABLE users MODIFY age SMALLINT;

-- rename a column
ALTER TABLE users CHANGE name full_name VARCHAR(100);
```

### DROP

Permanently removes the entire table including its structure and all data.

```sql
DROP TABLE users;
```

After this the table no longer exists at all.

### TRUNCATE

Removes all rows but keeps the table structure intact.

```sql
TRUNCATE TABLE users;
```

After this the table still exists but is completely empty.

| | DROP | TRUNCATE | DELETE |
|---|---|---|---|
| Removes data | yes | yes | yes |
| Removes table structure | yes | no | no |
| Can filter with WHERE | no | no | yes |
| Resets auto increment | yes | yes | no |

---

## Primary Key vs Foreign Key

### Primary Key

A primary key uniquely identifies each row in a table. It cannot be null and cannot have duplicates.

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

### Foreign Key

A foreign key is a column in one table that references the primary key of another table. It is used to link two tables together and enforce a relationship between them.

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    product VARCHAR(100),
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

Here `user_id` in the `orders` table points to `id` in the `users` table. This means you cannot insert an order with a `user_id` that does not exist in the `users` table.

| | Primary Key | Foreign Key |
|---|---|---|
| Purpose | Uniquely identifies a row in its own table | Links to a primary key in another table |
| Duplicates allowed | No | Yes |
| Null allowed | No | Yes |
| Number per table | Only one | Can have multiple |

A simple way to remember it is that the primary key is the ID badge of a table, and the foreign key is a reference to someone else's ID badge.

---

## String Patterns and Ranges

### LIKE with Wildcards

`LIKE` searches for a pattern inside a column. Two wildcards are available.

`%` matches any number of characters (including zero).

`_` matches exactly one character.

```sql
-- names that start with J
SELECT * FROM users WHERE name LIKE 'J%';

-- names that end with n
SELECT * FROM users WHERE name LIKE '%n';

-- names that contain "oh" anywhere
SELECT * FROM users WHERE name LIKE '%oh%';

-- names that are exactly 4 characters long
SELECT * FROM users WHERE name LIKE '____';

-- names that start with J and are exactly 4 characters
SELECT * FROM users WHERE name LIKE 'J___';
```

### Ranges with BETWEEN

```sql
-- age between 18 and 30 (inclusive)
SELECT * FROM users WHERE age BETWEEN 18 AND 30;

-- books priced between 10 and 50
SELECT * FROM BookShop WHERE PRICE BETWEEN 10 AND 50;
```

### IN

Matches against a list of values.

```sql
SELECT * FROM users WHERE department IN ('HR', 'Finance', 'IT');
```

---

## Sorting and Grouping

### ORDER BY

Sorts results in ascending or descending order.

```sql
-- ascending (default)
SELECT * FROM users ORDER BY age ASC;

-- descending
SELECT * FROM users ORDER BY age DESC;

-- sort by multiple columns
SELECT * FROM users ORDER BY department ASC, age DESC;
```

### GROUP BY

Groups rows that share the same value, usually used with aggregate functions.

```sql
SELECT department, COUNT(*) as total
FROM employees
GROUP BY department;
```

### HAVING

Filters results after grouping. It works like WHERE but operates on groups rather than individual rows.

```sql
-- only show departments with more than 5 employees
SELECT department, COUNT(*) as total
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

Using WHERE and HAVING together:

```sql
-- WHERE filters rows first, then GROUP BY groups them, then HAVING filters the groups
SELECT department, AVG(salary) as avg_salary
FROM employees
WHERE age > 25
GROUP BY department
HAVING AVG(salary) > 60000;
```

---

## Built-in Functions

### Aggregate Functions

Aggregate functions operate on a set of rows and return a single value.

```sql
COUNT(*)              -- total number of rows
COUNT(DISTINCT col)   -- number of unique values
SUM(col)              -- total sum
AVG(col)              -- average value
MAX(col)              -- highest value
MIN(col)              -- lowest value
```

Examples:

```sql
SELECT COUNT(*) FROM BookShop;
SELECT AVG(PRICE) FROM BookShop;
SELECT MAX(PRICE), MIN(PRICE) FROM BookShop;
SELECT SUM(PRICE) FROM BookShop WHERE PUBLISHED_DATE > '2020-01-01';
```

### Scalar and String Functions

Scalar functions operate on a single value and return a single value.

**String functions:**

```sql
-- convert to uppercase or lowercase
SELECT UPPER(name) FROM users;
SELECT LOWER(name) FROM users;

-- get the length of a string
SELECT LENGTH(name) FROM users;

-- trim whitespace from both sides
SELECT TRIM(name) FROM users;

-- extract part of a string (start position, length)
SELECT SUBSTRING(name, 1, 3) FROM users;

-- replace part of a string
SELECT REPLACE(email, '@gmail.com', '@yahoo.com') FROM users;

-- concatenate two strings
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM users;
```

**Date and time functions:**

```sql
-- get current date and time
SELECT NOW();
SELECT CURDATE();
SELECT CURTIME();

-- extract parts of a date
SELECT YEAR(PUBLISHED_DATE) FROM BookShop;
SELECT MONTH(PUBLISHED_DATE) FROM BookShop;
SELECT DAY(PUBLISHED_DATE) FROM BookShop;

-- difference between two dates in days
SELECT DATEDIFF('2024-12-31', '2024-01-01');

-- add or subtract time
SELECT DATE_ADD(PUBLISHED_DATE, INTERVAL 30 DAY) FROM BookShop;
SELECT DATE_SUB(PUBLISHED_DATE, INTERVAL 1 YEAR) FROM BookShop;

-- format a date for display
SELECT DATE_FORMAT(PUBLISHED_DATE, '%M %d, %Y') FROM BookShop;
```

**Numeric functions:**

```sql
-- round a number
SELECT ROUND(PRICE, 1) FROM BookShop;

-- round down or up
SELECT FLOOR(PRICE) FROM BookShop;
SELECT CEILING(PRICE) FROM BookShop;

-- absolute value
SELECT ABS(-15);
```
