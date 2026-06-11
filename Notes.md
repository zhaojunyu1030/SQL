# SQL Statements

## Data Definition Language (DDL)
Define, change, or drop data.

- `CREATE` Create tables and define its columns
- `ALTER` Altering tables (add / drop columns, and modify their datatypes, add / remove keys / constraints)
- `TRUNCATE` Delete data in a table but not the table itself
- `DROP` Delete tables

### CREATE
```
CREATE TABLE <table> (
    <column> <datatype> <optional parameters>,
    ...
);

Ex.
CREATE TABLE author (
    id CHAR(2) PRIMARY KEY NOT NULL,  # CHAR(2): char w/ fixed length 2
    name VARCHAR(15),                 # VARCHAR(15) char up to length 15
    country CHAR(2)
);
```

### ALTER TABLE ... ADD COLUMN
```
ALTER TABLE <table>
    ADD COLUMN <column> <datatype>
    ...
```

### ALTER TABLE ... ALTER COLUMN
```
ALTER TABLE <table>
    MODIFY <column> <datatype>;
```

### ALTER TABLE ... DROP COLUMN
```
ALTER TABLE <table>
    DROP COLUMN <column>;
```

### ALTER TABLE ... CHANGE
```
ALTER TABLE `<table>` CHANGE `<oldColumnName>` `<newColumnName>` <datatype>;  # change column name
```

### DROP TABLE
```
DROP TABLE <table>;
```

### TRUNCATE TABLE
```
TRUNCATE TABLE <table>
    IMMEDIATE;
```

## Data Manipulation Language (DML)
Read and modify data. CRUD operation (Create, Read, Update, and Delete rows)

- `INSERT` Insert a row or several rows into a table
- `SELECT` Read or select row or rows from a table
- `UPDATE` Edit row or rows in a table
- `DELETE` Remove a row or rows of data from a table

### SELECT
```
SELECT * FROM <table>;                     # select all rows
SELECT <column1>, <column2> FROM <table>;  # select only C1 and C2
```

### WHERE
```
SELECT <columns> FROM <table> WHERE <predicate>;
```

### COUNT, DISDINCT, and LIMIT and OFFSET
```
SELECT COUNT(*) FROM <table> WHERE <predicate>;
SELECT COUNT(DISTINCT <column>) FROM <table> WHERE <predicate>;  # count of unique entries
SELECT * FROM <table> LIMIT 25;                                  # only first 25 rows
SELECT * FROM <table> LIMIT 15 OFFSET 10;                        # first 15 rows starting from row 11
```

### INSERT
```
INSERT INTO <table>
    (<columns>)
VALUES
    ('<values>'),
    ('<values>');
```

### UPDATE
```
UPDATE <table>
SET <<column> = <value>>, <...>
WHERE <column> = '<value>';    # not using WHERE, every row will be modified
```

### DELETE
```
DELETE FROM <table>
WHERE <column> = '<value>';    # not using WHERE, every row will be removed

DELETE FROM <table>
WHERE <column> IN ('<value>', '<value>');
```

# Relational Model
Advantage is data independence.

## Entity-Relational Model (ER Model)
Tool to design relational databases.

Entities are independent objects which have Attributes. Attributes are characteristics of the entity.

Entities map to tables in a Relational Database.

Attributes map to columns in a table.

### PRIMARY KEY
Uniquely identifies each tuple or row in a table, preventing duplication of data and providing a way of defining relationships between tables.
```
CREATE TABLE <table> (
    <column> <datatype> PRIMARY KEY,
    ...
);

Ex.
CREATE TABLE BookShop_AuthorDetails (
    AUTHOR_ID INT PRIMARY KEY,
    AUTHOR_NAME VARCHAR(100),
    PRICE DECIMAL(5, 2) CHECK (PRICE >= 0)  # DECIMAL(5, 2): 5 digits total, 2 after decimal point
                                            # CHECK: constraint range of acceptable values
);
```

### FOREIGN KEY () REFERENCES ...
Primary keys defined in other tables, creating a link between tables.
```
CREATE TABLE <table2> (
    ...
    FOREIGN KEY (<column>) REFERENCES <table>
);

Ex.
CREATE TABLE BookShop (
    BOOK_ID INT PRIMARY KEY,
    TITLE VARCHAR(100),
    AUTHOR_ID INT,
    FOREIGN KEY (AUTHOR_ID) REFERENCES BookShop_AuthorDetails(AUTHOR_ID)
);
```

# Simplify SELECT Statement

## String Pattern

### WHERE ... LIKE ...
```
SELECT <column> FROM <table>
    WHERE <column> LIKE <condition>

Ex.
SELECT firstname FROM Author
    WHERE firstname LIKE 'R%'    # firstname starts with R
                         '%R'    # ends with R
                         '%R%'   # contains R anywhere
                         '_ _'   # exactly 2 char long
                         'R_ _'  # starts with R and is 2 char long
                                 # %: any number of char; _: exactly one char
```

## Range

### WHERE ... BETWEEN ... AND ...
```
SELECT <column> FROM <table>
    WHERE <condition>

Ex.
SELECT title, pages FROM Book
    WHERE pages BETWEEN 290 AND 300  # pages >= 290 and pages <= 300
```

## Set of Values

### WHERE ... IN ...
```
SELECT <column> FROM <table>
    WHERE country IN ('AU','BR')  # country = 'AU' or country = 'BR'
```

# Sort Result Sets

### ORDER BY
By defalt, the set is sorted by ascending order (A-Z)
```
SELECT <column> FROM <table>
    ORDER BY <column>
```

### DESC
Descending order (Z-A)
```
SELECT <column> FROM <table>
    ORDER BY <column> DESC
```

Specify Column Sequence Number
```
SELECT <column> FROM <table>
    ORDER BY <int>              # Ascending order by Column <int> (0-9)
```

# Group Result Sets

### GROUP BY, AS
```
SELECT <coulmn>, COUNT(<column>)
    AS <newColumn> FROM <table> GROUP BY <column>

Ex.
SELECT Country, COUNT(Country)
    AS Count FROM Author GROUP BY Country
```

```
# Count how many authors are from the same country
# Have to specify the name of second column

Country        Count
----------------------
AU               1
BR               1
CA               3
CN               6
```

# Restrict Result Sets
### HAVING
filters result AFTER grouping

WHERE filters result BEFORE grouping
```
Ex.
SELECT Country, COUNT(Country)
    AS Count FROM Author
    HAVING COUNT(Country) > 4
    GROUP BY Country
```

```
# Only displays country with Count > 4

Country        Count
----------------------
CN               6
```

# Built-In Functions

## Aggregate Functions
Input: Collection of values

Output: Single value

`SUM()`, `MIN()`, `MAX()`, `AVG()`, ...

### SUM()
```
SUM(<column>)

Ex.
SELECT SUM(COST) AS SUM_OF_COST FROM PETRESCUE
```

```
SUM_OF_COST
17.18
```

### MIN() / MAX()
```
SELECT MAX(QUANTITY) FROM PETRESCUE WHERE PET = `Dogs`
```

### AVG()
```
# Average cost per dog

SELECT AVG(COST / QUantity) FROM PETRESCUE
WHERE ANIMAL = `Dogs`
```

## Scalar and String Functions
Perform operations on individual values

`ROUND()`, `LENGTH()`, `UCASE`, `LCASE`

### ROUND()

Round to nearest integer
```
SELECT ROUND(COST) FROM PETRESCUE

SELECT ROUND(COST, 2) FROM PETRESCUE    # 2 decimal places after decimal point
```

### LENGTH()

Only for `CHAR` and `VARCHAR`

Retrieve length of value
```
SELECT LENGTH(ANIMAL) FROM PETRESCUE
```

### UCASE / LCASE

Retrieve value in uppercase / lowercase
```
SELECT UCASE(ANIMAL) FROM PETRESCUE
```

```
# use in WHERE clause, when not sure the value is stored in upper / lower / mixed

SELECT * FROM PETRESCUE
WHERE LCASE(ANIMAL) = `cat`
```

```
# use in DINSTINCT() func to get unique values

SELECT DISTINCT(UCASE(ANIMAL)) FROM PETRESCUE
```

# Date & Time Functions
DATE: YYYYMMDD

TIME: HHMMSS

TIMESTAMP: YYYYXXDDHHMMSSZZZZZZ (X: month, Z: ms)

`YEAR()`, `MONTH()`, `DAY()`, `DAYOFMONTH()`, `DAYOFWEEK()`, `DAYOFYEAR()`, `WEEK()`, `HOUR()`, `MINUTE()`, `SECOND()`

### DAY()

Extract DAY portion from a date
```
SELECT DAY(rescueDate) FROM petRescue
WHERE animal = `cat`
```

Get the number of rescues during the month of May
```
SELECT COUNT(*) FROM petRescue
WHERE MONTH(rescueDate) = `05`
```

## Date / Time arithmetic

### DATE_ADD()
What day is it 3 days after each rescue date
```
SELECT DATE_ADD(rescueDate, INTERVAL 3 DAY) FROM petRescue
```

### FROM_DAYS(), DATEDIFF(), CURRENT_DATE(), CURRENT_TIME()

`FROM_DAYS()`
Convert a day number into a date
```
SELECT FROM_DAYS(DATEDIFF(CURRENT_DATE, rescueDate)) FROM petRescue
```

# Sub-Queries
Cannot evaluate Aggregate functions in the WHERE clause
```
Ex.
SELECT salary FROM employees
        WHERE salary <
        (SELECT AVG(salary) FROM employees)
```

## Column Expressions
Substitute column name with a sub-query
```
Ex.
SELECT salary,
        (SELECT AVG(salary) FROM employees) AS avg_salary
FROM employees
```

## Table Expressions
Substitute the table name with a sub-query
```
Ex.
SELECT * FROM
        (SELECT emp_id, f_name, l_name, salary FROM employees) AS emp4all
```

## Multiple Tables
`WHERE` ... `IN`
```
SELECT * FROM employees
        WHERE dep_id IN
        (SELECT dept_id_dep FROM departments
                WHERE loc_id = `L002`)
```

## Access Multiple Tables with Implicit Join

### Full-Join
- Every row in the first table is joined with every row in the second table
- The result set has more rows than both tables
```
SELECT * FROM employees, departments
```

Additional opperands to limit the result set
```
SELECT * FROM employees, departments
        WHERE employees.dep_id = departments.dep_id_dp

# shorter

SELECT * FROM employees E, departments D
        WHERE E.dep_id = D.dep_id_dp
```

```
SELECT emp_id, dep_name
        FROM employees E, departments D
        WHERE E.dep_id = D.dep_id_dp

# column names can be pre-fixed

SELECT E.emp_id, D.dep_name FROM employees E, departments D
        WHERE E.dep_id = D.dep_id_dp
```

# Access Databases Using Python (DB-API)

## Connection Methods
- `.cursor()` Returns a new cursor object using the connection
- `.commit()` Commits any pending transaction to the database
- `.rollback()` Causes the database to rollback to the start of any pending transaction
- `.close()` Close a database connection

## Cursor Methods
Manage the content of a fetch operation
- `.callproc()`
- `.execute()`
- `.executemany()`
- `.fetchone()`
- `.fetchmany()`
- `.fetchall()`
- `.nextset()`
- `.arraysize()`
- `.close()`

## Writing Code Using DB-API

### .connect()
```
import sqlite3
conn = sqlite3.connect('instructor.db')
```

### .cursor()
```
cursor_obj = conn.cursor()
```

### .execute()
```
cursor_obj.execute('''<SQL statement>''')
```

### .fetchall()
```
statement = <SQL statement>
cursor_obj.execute(statement)

output_all = cursor_obj.fetchall()
for row_all in output_all:
    print(row_all)
```

### .fetchmany()
```
statement = <SQL statement>
cursor_obj.execute(statement)

output_many = cursor_obj.fetchmany(2)
for row_many in output_many:
    print(row_many)
```

### .read_sql_query()
```
df = pd.read_sql_query("<SQL statement>", conn)
```

### .shape
```
df.shape
```

### .close()
```
conn.close()
```

```
from dbmodule import connect

# Create connection object
connection = connect('databasename', 'username', 'pswd')

# Create a cursor object
cursor = connection.cursor()

# Run queries
cursor.execute(`SELECT * FROM mytable`)
results = cursor.fetchall()

# Free resourses
cursor.close()
connection.close()
```

```
Ex.

import sqlite3

conn = sqlite3.connect("INSTRUCTOR.db")
cursor_obj = conn.cursor()
```

```
# create table

cursor_obj.execute("DROP TABLE IF EXISTS INSTRUCTOR")
table = '''CREATE TABLE IF NOT EXISTS INSTRUCTOR(
                  ID INTEGER PRIMARY KEY NOT NULL,
                  FNAME VARCHAR(20)
                  LNAME VARCHAR(20)
                  CITY VARCHAR(20)
                  CCODE CHAR(2));'''
cursor_obj.execute(table)
```

```
# insert data

cursor_obj.execute('''INSERT INTO INSTRUCTOR
                      VALUES (1, 'Rav', 'Ahuja', 'TORONTO', 'CA'),
                                (2, 'Raul', 'Chong', 'Markham', 'CA'),
                                (3, 'Hima', 'Vasudevan', 'Chicago', 'US')''')
```

```
# fetch all rows

statement = '''SELECT * FROM INSTRUCTOR'''
cursor_obj.execute(statement)

output_all = cursor_obj.fetchall()
for row_all in output_all:
    print(row_all)
```

```
# fetch few rows

statement = '''SELECT * FROM INSTRUCTOR'''
cursor_obj.execute(statement)

output_few = cursor_obj.fetchmany(2)
for rows_few in output_few:
    print(rows_few)
```

```
# fetch only FNAME

statement = '''SELECT FNAME FROM INSTRUCTOR'''
cursor_obj.execute(statement)

output_name = cursor_obj.fetchall()
for rows_name in output_name:
    print(rows_name)
```

```
# update table

statement = '''UPDATE INSTRUCTOR SET CITY="MOOSETOWN" WHERE FNAME="Rav"'''
cursor_obj.execute(statement)

statement = '''SELECT * FROM INSTRUCTOR'''
cursor_obj.execute(statement)
  
print("All the data")
output1 = cursor_obj.fetchmany(2)
for row in output1:
    print(row)
```

```
# use in pandas dataframe

import pandas as pd

df = pd.read_sql_query("SELECT * FROM INSTRUCTOR", conn)
df

df.LNAME[0]

df.shape

conn.close()
```
# Magic Statements
Shortcuts that control how the notebook behaves

Install ipython-sql
```
!pip install --user ipython-sql
```
Enable SQL Magic in Jupyter notebook
```
%load_ext sql
```

## Line Magics
Commands that are prefixed with `%` and operate on a single line of input

- `%pwd` print the current working directory
- `%ls` list all the files in current directory
- `%history` show all the command history
- `%reset` reset the namespace by removing all names defined by the user
- `%who` list all variables in the namespace
- `%whos` more detailed information
- `%matplotlib inline` make matplotlib plots appear within the notebook
- `%timeit` times the execution of a single statement
- `%lsmagic` list all available line magics

Time for executing single statement
```
Ex.
%timeit <statement>
```

## Cell Magics
Commands that are prefixed with `%%` and operate on multiple lines of input

Allows to write code in other languages
- `%%HTML` Write HTML code in code and render it
- `%%javascript` Write JavaScript code in cells
- `%%bash cell` Write bash commands

Time for executing the whole cell
```
Ex.
%%timeit
<statement1>
<statement2>
<statement3>
```

## Access SQLite Databases with SQL Magics
```
# 1. Install and load everything

!pip install ipython-sql
%load_ext sql    # load the external module
```

```
# 2. Connect to a database

import sqlite3
conn = sqlite3.connect('HR.db')
%sql sqlite:///HR.db    # connect SQL Magic module to the SQL server being accessed
```

```
# 3. Run SQL using line magic for a single line

%sql SELECT * FROM Employee
```

```
# 4. Run SQL using cell magic for multiple lines

%%sql
SELECT *
FROM Employees
WHERE City = "Chicago"
```

```
# 5. Save query result into a Python variable

result = %sql SELECT * FROM Employees
df = result.DataFrame()
print(df)
```

# Analyze Data with Python

## Pandas

### .read_csv()
```
import pandas
df = pd.read_csv("<csv file>")
```

### .to_sql()
```
import pandas
df = pd.read_csv("<csv file>")
df.to_sql("<table>", conn, if_exists='replace', index=False)
```

### .read_sql()
```
df = pd.read_sql("<SQL statement>", conn)
```

## Seaborn

### .barplot()
```
import seaborn as sns
sns.barplot(x='Test_Score', y='Frequency', data=df)
```

## Load CSV to SQLite3 with Pandas
```
import pandas as pd
import sqlite3

data = pd.read_csv('./menu.csv')          # read database
conn = sqlite3.connect('McDonalds.db')    # connection object
data.to_sql('MCDONALDS_NUTRITION', conn)    # data loaded as a table to the database

# Use Pandas to retrieve data from database tables

df = pd.read_sql("SELECT * FROM MCDONALDS_NUTRITION", conn)
print(df)

df.head()    # View the first rows
df.describe(include='all')    # summary statistics of data

# Plot data

import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns

plot = sns.swarmplot(x='Category', y='Sodium', data=df)
plt.setp(plot.get_xticklabels(), rotation=70)
plt.title('Sodium Content')
plt.show()

# Which item has maximum sodium content
df['Sodium'].describe()    # maximum value
df['Sodium'].idxmax()    # idx val at which the max val is obtained
df.at[82, 'Item']    # the associated item name

# Further exploration (correlation between protein and total fat)

plot = sns.jointplot(x="Protein", y="Total Fat", data=df)
plot.show()
```
