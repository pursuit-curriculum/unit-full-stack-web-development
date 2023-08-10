# Intro to Databases/SQL Part 1

## Lesson Objectives

After this lesson, you should be able to:

- Define databases and their role in a full-stack app
- Introduction to PostgreSQL
- Differentiate between data definition language (DDL) and data manipulation language (DML).
- Connect to the `psql` shell via the command line.
- Perform common commands in the `psql` shell.
- Set up and drop (delete) a (sub) database
- Perform the following actions with tables:
- Create a table, and set columns with
  - datatypes
  - constraints
- Alter the table.
- Drop the table.
- Perform the following actions with table data:
  - **C**reate a row and put it into the table (insert data)
  - **R**ead from the table (query data)
  - Filter data using the `WHERE` clause.
  - **U**pdate from the table
  - **D**elete from the table
- Write more complex queries that:
  - Limit
  - Sort
  - Aggregate

---

## Introduction

- What is a database?
- Why do you need to connect a database to a server?
- What is PostgreSQL?

### Getting Started

You should have already downloaded PostgreSQL at the start of this course. [Pursuit Core Environment Set Up](https://github.com/pursuit-curriculum-resources/guide-computer-setup/tree/main/postgresql)

The application should already be running. You should be able to enter the Postgres shell by typing:

- `psql` anywhere in Terminal.

## SQL Syntax

Which is the correct convention for SQL keywords?

```SQL
-- first
SELECT * FROM bookmarks;

-- second
select * from bookmarks;
```

Will you get an error if you don't capitalize correctly?

**Note:** Comments start with two dashes `--`

## SQL's big syntax gotchas

- What status does the following prompt let you know?

![](../assets/prompt-ready.png)

- What status does the following prompt let you know?

![](../assets/prompt-forgot-semi-colon.png)

- How can you fix this prompt back to a ready state?

- How do you quit out of a view with lots of data?

![](../assets/q-out-of-view.png)

## Create a Database

Let's create a database and then drop (delete) it. Then we'll create a new one, connect it, and use it for the rest of this lesson.

```SQL
-- Create the sub-database bar
CREATE DATABASE bar;

-- drop it
DROP DATABASE bar;

-- Get started with our code along
CREATE DATABASE lesson_db;

-- connect to the lesson_db sub database
\connect lesson_db;

-- OR (does the same thing as connect, just shorthand)
\c lesson_db;
```

## Data types

Here are some of the most common datatypes for PostgreSQL. Define what each one is

1. `INT`
1. `DECIMAL`
1. `BOOL`
1. `VARCHAR(n) `
1. `TEXT`
1. `TIMESTAMP`
1. `SERIAL`

## Create a Table

Inside a database, we can have many tables. We'll create a table of houses for sale. If we were working for a real estate company, we would have separate tables for buyers, agents, commercial properties, etc.

Tables have columns and rows. We will define the names of the columns and their data types.

```SQL
-- Demo create, see and drop
CREATE TABLE foo ( name TEXT ); -- create a table called 'foo' with one column called 'name', which is a small text column

-- see table
\dt

-- Drop the table
DROP TABLE foo;

```

Use the following table for the rest of the class activity.

```SQL

-- 'houses' table has an id column `serial`, which increases with each addition, and columns for address, city, state, price, and boolean properties for pool and for_sale.

CREATE TABLE
 homes
 ( id SERIAL PRIMARY KEY, address TEXT NOT NULL, city TEXT, st VARCHAR(2), price INT, pool BOOLEAN DEFAULT false, for_sale BOOLEAN);

-- Show description of columns for the table homes
\d homes;
```

- What is the purpose of the `id` field?

- What is the keyword `serial`?

- What does `PRIMARY KEY` do?

- What does the constraint `NOT NULL` do?

- What is the difference between `TEXT` and `VARCHAR(n)`?

- When is it preferable to use `VARCHAR(n)` over `TEXT`?

- What does they keyword `DEFAULT` do?

### Alter a Table

You can make changes to the table you've created.

**IMPORTANT:** You cannot roll back changes or undo deletes with a PostgreSQL database. When working in production, be sure to have backup systems in place.

```SQL
-- Rename a table
ALTER TABLE homes RENAME TO houses;

-- Add a test string column
ALTER TABLE houses ADD COLUMN test TEXT;

-- Drop the test column
ALTER TABLE houses DROP COLUMN test;

-- Rename a column
ALTER TABLE houses RENAME st TO state;

```

See the columns in the house's table again.

```SQL
\d+ houses
```

## Insert Into The Table

You don't have to remember the order of the columns you created, but you have to match the order when inserting:

```SQL
INSERT INTO
 houses (address, pool, price, state, city, for_sale)
VALUES
('99 Sunnyside Drive', TRUE, 100, 'NY', 'Springfield', true);
```

- How can you see the data in your table?

```SQL
SELECT * FROM houses;
```

- What does the `*` (star) mean?

- Create a new row. Will the following insertion work, or will it error?

```SQL
INSERT INTO
 houses (address)
VALUES
('Graceland');
```

Remember, you can use the `up` arrow to scroll to previous commands to run.

```SQL
SELECT * FROM houses;
```

Let's add some more houses (copy-paste from the notes into your Terminal):

```SQL
INSERT INTO
 houses (address, city, state, price, pool, for_sale)
VALUES
 ('2 Maple Court', 'Monroe', 'NY', 200, false, true),
 ('50 Beech Street', 'Bacon', 'IN', 75, null , null),
 ('101 Willow Avenue', 'Twin Peaks', 'WA', 300, null, null),
 ('2300 Orchard Lane', 'Monroe', 'NC', 150, null, true),
 ('57 Sycamore Boulevard', 'Monroe', 'CT', 275, false, false),
 ('88 Hemlock Grove', 'Eek', 'AK', 200, true, true),
 ('109 Pine Drive', 'Happyland', 'CT', 220, false, null);
```

### Select from the table

Here, we can explore some powerful queries we can make using SQL.

```SQL
-- Select all rows from the house's table. display only the address column
SELECT address FROM houses;

-- Select all rows from the house's table. Display only the address and state column
SELECT address, state FROM houses;

 -- Select all rows from the house's table. display only all the columns
SELECT * FROM houses;

-- Select all rows from the house's table where the name column is set to 'Monroe'
SELECT * FROM houses WHERE city = 'Monroe';

-- Select all rows from the house's table where the name column is set to 'ny' or 'Ny' or 'NY' (case insensitive)
SELECT * FROM houses WHERE state ILIKE 'NY';

-- Select all rows from the house's table where the name column contains 'Drive'
SELECT * FROM houses WHERE address LIKE '%Drive%';

-- Select all rows from the house's table where the city column is set to 'Monroe' AND the state column is set to 'CT'
SELECT * FROM houses WHERE city = 'Monroe' AND state = 'CT';

-- Select all rows from the house's table where either the pool column is set to TRUE OR the city column is set to 'Twin Peaks'
SELECT * FROM houses WHERE pool = TRUE OR city = 'Twin Peaks';

-- Select all rows from the house's table where the price is set to 200
SELECT * FROM houses WHERE price = 200;

-- Select all rows from the house's table where the price column is not set to 180
SELECT * FROM houses WHERE price != 180;

-- Select all rows from the house's table where the price column is greater than 165
SELECT * FROM houses WHERE price > 165;

 -- Select all rows from the house's table where the price column is less than 165
SELECT * FROM houses WHERE price < 165;

-- Select all rows from the house's table where the price column is greater than or equal to 165
SELECT * FROM houses WHERE price >= 165;

-- Select all rows from the house's table where the price column is less than or equal to 165
SELECT * FROM houses WHERE price <= 165;

-- Select all rows from the house's table where the price column is null
SELECT * FROM houses WHERE price IS NULL;

-- Select all rows from the house's table where the pool column has a value
SELECT * FROM houses WHERE pool IS NOT NULL;

```

## Update a Row

Use the keyword `UPDATE` to update one or more rows.

```SQL
UPDATE houses SET pool = TRUE WHERE id = 7;
```

Typically actions like creating a new item or updating and deleting an item don't return any rows.

However, sometimes we want to see the changes we made. We can add a `RETURNING` statement. This saves us from making a follow-up query if we use the data immediately.

```SQL
UPDATE houses SET for_sale = TRUE WHERE id = 9 RETURNING *;
```

## Delete a Row

```SQL
DELETE FROM houses WHERE id = 1;
```

> **NOTE**: DANGER! Forgetting the `WHERE` clause for Update or DELETE can cause every row to update/delete.

## Delete Many Rows and See the Address

```SQL
DELETE FROM houses WHERE pool = false RETURNING address, state;
```

## Quit PostgreSQL Shell

To quit `psql`, type `\q`.

## Bonus lesson content

### Limit

Imagine the database had 10,000 entries. What is the benefit of a `LIMIT` clause?

```SQL
-- Select all rows from the house's table, but show only the first row
SELECT * FROM houses LIMIT 1;
```

### Offset

Imagine this data was (again, 10,000+ rows big) and being used for a real estate website. What would benefit from using the `OFFSET` and `LIMIT` clauses?

- When you visit a website with many items, does it typically load all the items at once? Why or why not?

```SQL
-- For comparison to the next one
SELECT * FROM houses;
-- Select all rows from the house's table, but show only one row. Skip the first row
SELECT * FROM houses LIMIT 1 OFFSET 1;

```

### Sorting

If you want to sort your data, you should rely on `ORDER BY` and not the order you think the rows are in. Why?

```SQL
-- Select all rows from the houses table, order by name alphabetically
SELECT * FROM houses ORDER BY city ASC;

-- Select all rows from the houses table, order by name reverse alphabetically
SELECT * FROM houses ORDER BY city DESC;

-- Select all rows from the houses table, order by price ascending
SELECT * FROM houses ORDER BY price ASC;

-- Select all rows from the houses table, order by price descending
SELECT * FROM houses ORDER BY price DESC;
```

### Combination

Of the following statements, which is a valid order?

```SQL
SELECT address, city, state FROM houses ORDER BY city, state ASC LIMIT 2 OFFSET 2;
```

```SQL
 ORDER BY city, state ASC LIMIT 2 OFFSET 2 SELECT address, city, state FROM houses;
```

```SQL
SELECT address, city, state FROM houses state ASC LIMIT 2 OFFSET 2 ORDER BY city;
```

What kind of messages do you get when you create statements that are out of order?

### Counts and Aggregation

```SQL
-- show the total number of houses.
SELECT COUNT(price) FROM houses;

-- Divide all rows into groups by city and state. Show the number of rows in each group. Also, show the city and state of each group
SELECT COUNT(*) address, city, state FROM houses GROUP BY city, state;

 -- Show the SUM of all the house prices.
SELECT SUM(price) FROM houses WHERE price IS NOT NULL;

 -- Show the SUM of all the house prices where the pool is true
SELECT SUM(price) FROM houses WHERE pool IS TRUE;

-- Divide all rows into groups by whether or not they are for sale. Show the AVG of the price of each group. Also, show the for_sale property of each group
SELECT AVG(price), for_sale FROM houses GROUP BY for_sale;

-- show the MIN price of houses.
SELECT MIN(price) FROM houses;

-- Divide all rows into groups by for_sale. Show the MIN of the price of each group. Also, show the for_sale of each group.
SELECT MAX(price), for_sale FROM houses GROUP BY for_sale;

```
