# SQL Fundamentals — TryHackMe Room Notes

## Syntax Reference Table

| Syntax / Statement | Example | Description |
|---|---|---|
| `CREATE DATABASE` | `CREATE DATABASE database_name;` | Creates a new database. |
| `SHOW DATABASES` | `SHOW DATABASES;` | Lists all databases present on the server. |
| `USE` | `USE database_name;` | Sets the specified database as the active one for subsequent queries. |
| `DROP DATABASE` | `DROP DATABASE database_name;` | Deletes a database entirely. |
| `CREATE TABLE` | `CREATE TABLE table_name (col1 TYPE, col2 TYPE);` | Creates a new table with defined columns and data types. |
| `SHOW TABLES` | `SHOW TABLES;` | Lists all tables in the currently active database. |
| `DESCRIBE` / `DESC` | `DESCRIBE table_name;` | Shows the columns, data types, and key info of a table. |
| `ALTER TABLE` | `ALTER TABLE table_name ADD column_name TYPE;` | Modifies an existing table (add/rename/remove columns, change data types). |
| `DROP TABLE` | `DROP TABLE table_name;` | Deletes a table entirely. |
| `INSERT INTO` | `INSERT INTO table (col1, col2) VALUES (val1, val2);` | **Create** — adds a new record to a table. |
| `SELECT` | `SELECT * FROM table;` | **Read** — retrieves records (all columns with `*`, or specific columns by name). |
| `UPDATE ... SET ... WHERE` | `UPDATE table SET col = value WHERE condition;` | **Update** — modifies existing record(s) matching a condition. |
| `DELETE FROM ... WHERE` | `DELETE FROM table WHERE condition;` | **Delete** — removes record(s) matching a condition. |
| `WHERE` | `SELECT * FROM table WHERE condition;` | Filters records based on a condition. |
| `DISTINCT` | `SELECT DISTINCT column FROM table;` | Returns only unique values, removing duplicates. |
| `GROUP BY` | `SELECT column, COUNT(*) FROM table GROUP BY column;` | Groups rows sharing a value, often used with aggregate functions. |
| `ORDER BY ... ASC/DESC` | `SELECT * FROM table ORDER BY column DESC;` | Sorts results in ascending (`ASC`) or descending (`DESC`) order. |
| `HAVING` | `... GROUP BY column HAVING condition;` | Filters grouped results after aggregation (unlike `WHERE`, which filters before). |
| `LIKE` | `WHERE column LIKE "%text%"` | Matches a pattern within a column (`%` = wildcard). |
| `AND` | `WHERE cond1 AND cond2` | True only if **all** conditions are true. |
| `OR` | `WHERE cond1 OR cond2` | True if **at least one** condition is true. |
| `NOT` | `WHERE NOT condition` | Reverses/negates a condition. |
| `BETWEEN` | `WHERE column BETWEEN val1 AND val2` | Checks if a value falls within a range (inclusive). |
| `=` | `WHERE column = value` | Equal to. |
| `!=` | `WHERE column != value` | Not equal to. |
| `<` | `WHERE column < value` | Less than. |
| `>` | `WHERE column > value` | Greater than. |
| `<=` | `WHERE column <= value` | Less than or equal to. |
| `>=` | `WHERE column >= value` | Greater than or equal to. |
| `CONCAT()` | `SELECT CONCAT(col1, " ", col2);` | Joins two or more strings together. |
| `GROUP_CONCAT()` | `GROUP_CONCAT(name SEPARATOR ", ")` | Concatenates values from multiple rows into a single string. |
| `SUBSTRING()` | `SUBSTRING(column, start, length)` | Extracts part of a string starting at a given position. |
| `LENGTH()` | `LENGTH(column)` | Returns the number of characters in a string. |
| `COUNT()` | `SELECT COUNT(*) FROM table;` | Returns the number of matching records. |
| `SUM()` | `SELECT SUM(column) FROM table;` | Adds up all (non-NULL) values in a column. |
| `MAX()` | `SELECT MAX(column) FROM table;` | Returns the highest value in a column. |
| `MIN()` | `SELECT MIN(column) FROM table;` | Returns the lowest value in a column. |

## Short Notes

- **Relational vs Non-relational databases**: Relational (SQL) databases store structured data in tables with a fixed schema — best when data format is consistent and accuracy matters (e.g., e-commerce transactions). Non-relational (NoSQL) databases store data in flexible, non-tabular formats (e.g., JSON-like documents) — best when incoming data varies greatly in structure (e.g., social media content).
- **Tables, rows, columns**: A table holds records; each column defines a field and its data type (String, Integer, Float/Decimal, Date/Time); each inserted record becomes a row.
- **Primary Key**: Uniquely identifies each record in a table — only **one** primary key column per table.
- **Foreign Key**: A column that references a primary key in another table, creating a relationship between tables — a table **can have multiple** foreign keys.
- **DBMS**: Software (e.g., MySQL, MongoDB, Oracle DB, MariaDB) that acts as the interface between the end user and the database.
- **SQL benefits**: Fast (efficient storage/processing), easy to learn (plain-English syntax), reliable (enforces strict data structure), and flexible (powerful querying/analysis capability).
- **CRUD summary**:
  - **C**reate → `INSERT INTO`
  - **R**ead → `SELECT`
  - **U**pdate → `UPDATE ... SET ... WHERE`
  - **D**elete → `DELETE FROM ... WHERE`
- **WHERE vs HAVING**: `WHERE` filters rows *before* grouping/aggregation; `HAVING` filters groups *after* aggregation (e.g., after `GROUP BY` + `COUNT()`).
- **Wildcard matching**: The `%` symbol in `LIKE "%text%"` matches any sequence of characters before/after the given text.
- **AUTO_INCREMENT**: Commonly paired with an `INT PRIMARY KEY` column so each new record automatically gets the next sequential ID.
- **NOT NULL**: A column constraint that rejects any insert attempt where that field is left empty.
- **Default MySQL system databases**: `mysql`, `information_schema`, `performance_schema`, and `sys` come built in and support MySQL's internal functioning (not user data).
