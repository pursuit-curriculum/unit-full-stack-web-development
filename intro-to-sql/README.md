# Intro to Databases/SQL Part 1

## Lesson Objectives

- Define databases and their role in a full-stack app
- Introduction to PostgreSQL
- Differentiate between data definition language (DDL) and data manipulation language (DML).
- Connect to the `psql` shell via the command line.
- Perform common commands in the `psql` shell.
- Set up and drop (delete) a (sub) database
- Create a table, set columns, and drop the table
- **C**reate a row and put it into the table (insert data)
- **R**ead from the table (query data)
  - Filter data using the `WHERE` clause.
- **U**pdate from the table
- **D**elete from the table
- Additional features:
  - Limit,
  - Sorting,
  - Aggregation

Implement common data types in DDL statements including auto-increment IDs.
Perform CRUD actions via DML.

## Introduction

### What is a Database?

A database is an organized collection of data stored and accessed electronically.

For your CRUD apps, you've been hard-coding some data in order to learn the fundamentals of servers and RESTFul routes. You could make some temporary changes, but the changes would disappear as soon as you shut down or restarted the server.

You need a way to make the data persist so that you will still have the data if the server is restarted or shut down. To do this, you'll use a database to store and access data.

There are many databases. A common type is a relational database, which stores data in tables and rows, much like an Excel or Google spreadsheet. You will interact with relational databases using a coding language called SQL (Structured Query Language). Some popular ones are MySQL, Oracle, SQL Server, SQLite, and PostgreSQL.

While a Google sheet can hold hundreds or thousands of records, it isn't meant to hold millions or billions of records (think about a database that contains all the user data of FaceBook). Additionally, you can relate different tables (sheets), which allows you to do very powerful things with data. You'll get to see this in action in the next lesson.

You'll be working with PostgreSQL (a successor to a database called `ingres`), an open-source RDBMS (relational database management system) created at the University of California Berkeley. It started being built in 1982.

The [skeuomorphic representation of a database](https://en.wikipedia.org/wiki/Skeuomorph) is a stack of disks:

![](./assets/noun_database_2262303.png)

#### DDL and DML

When learning and and communicating about a particular technology, it's important to learn the terminology so you can understand and share ideas. There are two terms that are helpful to know as you learn about databases.

DDL (Data Definition Language) - All of the statements will involve defining the data (working with the data tables). You can remember this by the mnemonic device DR.CAT.

- Drop (a table)
- Rename(a table)
- Create (a table)
- Alter (a table)
- Truncate (a table)

DML (Data Manipulation Language)- All of the statements will involve manipulating the actual data within a table. You can remember this by the mnemonic device SUDI.

- Select
- Update
- Insert
- Delete

In both DDL and DML, can you figure out which actions related to each CRUD action?

> **Note**: [Truncate](<https://en.wikipedia.org/wiki/Truncate_(SQL)>) a table means resetting all the data in the table. In some definitions it is related with DML and others it is related to DDL. Why is this particular action seen as part of DDL or DML?

## Getting Started

You should have already downloaded PostgreSQL at the start of this course. [Pursuit Core Environment Set Up](https://github.com/pursuit-curriculum-resources/guide-computer-setup/tree/main/postgresql)

It should already be running, and you should see an icon of an elephant in your Mac's bar.

![](./assets/postgres-app-icon.png)

If you don't see it, but you've installed it, use `spotlight` <kbd>command</kbd> <kbd>spacebar</kbd> to search and load it.

![](./assets/spotlight-postgres.png)

You can configure PostgreSQL to always load on startup and stop/start your server from the icon.

![](./assets/postgres-icon-details.png)

Today, you're going to use the PostgreSQL shell in the terminal. To launch it, go _anywhere_ in the terminal and type:

- `psql`

![](./assets/launch-psql.png)

## SQL Syntax

Even though keywords in SQL are not case-sensitive, the convention is to capitalize them.

```sql
-- correct
SELECT * FROM bookmarks;

-- incorrect
select * from bookmarks;
```

**Note:** Comments start with two dashes `--`

## SQL's Big GOTCHAS

Postgres in the terminal gives you some cues, but they can be hard to spot as a new user.

A prompt ready to go is preceded by `=#`.

![](./assets/prompt-ready.png)

Semi-colons are required to end your statement. If you forget your semi-colon, the prompt will drop to the following line and appear as a `-#`.

![](./assets/prompt-forgot-semi-colon.png)

To fix this, add a semi-colon and press <kbd>return<kbd>

![](./assets/enter-semi-colon-to-end-statement.png)

Another thing that can happen is that you get a lot of rows of data back, and you may not want to scroll through them all. To `q`uit that view, press `q`.

You'll see a colon that will let you know there is more data, which you can use the down arrow to scroll through or press `q` to get your prompt back.

![](./assets/q-out-of-view.png)

## Create a Database

PostgreSQL is a database. When you run the application, you can create `sub-databases` that allow you to work on different projects. For example, you may have one for your bookmarks app, one for your budgeting app, and more. These sub-databases are most often referred to as `databases`, and you use the keyword `databases` to create them.

Create a database and then drop (delete) it. Then we'll create a new one, connect it, and use it for the rest of this lesson.

```SQL
-- create the sub-database second
CREATE DATABASE first;

-- drop it
DROP DATABASE first;

-- get started with our code along
CREATE DATABASE pre_reading_code_along;

-- connect to the pre_reading_code_along sub database
\connect pre_reading_code_along;

-- OR (does the same thing as connect, just shorthand)
\c pre_reading_code_along;
```

## Data types

As you've been using Express and creating data as arrays inside objects, we could play it fast and loose with datatypes. We could enter `4` as `4` (a number) or `'4'` - a string. JavaScript didn't care.

PostgreSQL is more strict and expects the correct data types in the columns we will create. Here are some of the most common ones:

1. `INT` - [Whole number](https://www.postgresql.org/docs/current/datatype-numeric.html)
1. `DECIMAL` - [float/decimal](https://www.postgresql.org/docs/current/datatype-numeric.html)
1. `BOOL` - [boolean](https://www.postgresql.org/docs/current/datatype-boolean.html)
1. `VARCHAR(n)` - [Set character limit](https://www.postgresql.org/docs/current/datatype-character.html)
1. `TEXT` - [No character limit (text size up to 1gb)](https://www.postgresql.org/docs/current/storage-toast.html)
1. `TIMESTAMP` - [date](https://www.postgresql.org/docs/current/datatype-datetime.html)

## Create a Table

Inside a database, we can have many tables. We'll create a table of dvds for sale. If we were working for a real estate company, we would have separate tables for buyers, agents, commercial properties, etc.

Tables have columns and rows. We will define the names of the columns and their data types.

```SQL
-- describe your tables
CREATE TABLE second ( name TEXT ); -- create a table called 'second' with one column called 'name', which is a  text column

-- see table
\dt

-- drop a table
DROP TABLE second;

-- 'DVD' table has an id column `serial`, a number that increases with each addition, and columns for title, company, rating, price, and boolean properties for discontinued.

CREATE TABLE
 VHS
 ( id serial, title TEXT NOT NULL, company TEXT, ratingg VARCHAR(4), price INT, discontinued BOOLEAN DEFAULT false);

-- show description of columns for the table dvds
\d VHS;
```

Additionally, you are adding an `id` - an id is a helpful field. All of our data is subject to change. The price can change, or there can be a typo in the title that needs fixing. An id is a unique identifier for each row. The keyword [serial](https://www.postgresql.org/docs/9.2/datatype-numeric.html) provides this functionality of increasing the id number automatically.

![](./assets/create-and-see-table.png)

### Alter a Table

You can make changes to the table you've created.

**IMPORTANT:** You cannot roll back changes or undo deletes with a PostgreSQL database. When working in production, be sure to have backup systems in place.

```sql
-- add a test string column
ALTER TABLE VHS ADD COLUMN test TEXT;

-- drop the test column
ALTER TABLE VHS DROP COLUMN test;

-- rename a column
ALTER TABLE VHS RENAME ratingg TO rating;

-- rename a table
ALTER TABLE VHS RENAME TO dvds;
```

See the columns in the dvd's table again.

```sql
\d+ dvds
```

## Insert Into The Table

You don't have to remember the order of the columns you created, but you have to match the order when inserting:

```SQL
INSERT INTO
 dvds (title, discontinued, price, rating, company)
VALUES
('Hackers', TRUE, 100, 'PG13', 'United Artists');
```

**Success** will look something like

```SQL
INSERT 0 1
```

You can see the data by doing a query. The `*` (star) means show all the columns.

```SQL
SELECT * FROM dvds;
```

You don't have to enter all the fields (only the required ones)

```SQL
INSERT INTO
 dvds (title)
VALUES
('Tron');
```

Remember, you can use the `up` arrow to scroll to previous commands to run.

```SQL
SELECT * FROM dvds;
```

Again to see the new entry.

Let's add some more dvds (copy-paste from the notes into your terminal):

```SQL
INSERT INTO
 dvds (title, company, rating, price, discontinued)
VALUES
 ('Imitation Game', 'Black Bear', 'PG13', 12, false),
 ('Matrix', 'Warner Bros', 'R', 4, null ),
 ('Tetris', 'Apple', 'R', 19, null),
 ('2001: A Space Odyssey', 'Stanley Kubrick Productions', 'G', 150, true),
 ('Hidden Figures', 'Fox 2000', 'PG', 25, false);
```

### Select from the table

Here, we can explore some powerful queries we can make using SQL.

```SQL
-- select all rows from the dvd's table. display only the title column
SELECT title FROM dvds;

-- select all rows from the dvd's table. Display only the title and rating column
SELECT title, rating FROM dvds;

 -- select all rows from the dvd's table. display only all the columns
SELECT * FROM dvds;

-- select all rows from the dvd's table where the company column is set to 'Apple'
SELECT * FROM dvds WHERE company = 'Apple';

-- select all rows from the dvd's table where the rating column is set to 'pg' or 'Pg' or 'PG' (case insensitive)
SELECT * FROM dvds WHERE rating ILIKE 'pg';

-- select all rows from the dvd's table where the rating column contains 'PG'
SELECT * FROM dvds WHERE rating LIKE '%PG%';

-- select all rows from the dvd's table where the company column is set to 'Apple' AND the rating column is set to 'R'
SELECT * FROM dvds WHERE company = 'Apple' AND rating = 'R';

-- select all rows from the dvd's table where either the discontinued column is set to TRUE OR the company column is set to 'Apple'
SELECT * FROM dvds WHERE discontinued = TRUE OR company = 'Apple';

-- select all rows from the dvd's table where the price is set to 100
SELECT * FROM dvds WHERE price = 100;

-- select all rows from the dvd's table where the price column is not set to 100
SELECT * FROM dvds WHERE price != 100;

-- select all rows from the dvd's table where the price column is greater than 25
SELECT * FROM dvds WHERE price > 25;

 -- select all rows from the dvd's table where the price column is less than 25
SELECT * FROM dvds WHERE price < 25;

-- select all rows from the dvd's table where the price column is greater than or equal to 25
SELECT * FROM dvds WHERE price >= 25;

-- select all rows from the dvd's table where the price column is less than or equal to 25
SELECT * FROM dvds WHERE price >= 25;

-- select all rows from the dvd's table where the price column is null
SELECT * FROM dvds WHERE price IS NULL;

-- select all rows from the dvd's table where the discontinued column has a value
SELECT * FROM dvds WHERE discontinued IS NOT NULL;

```

## Update a Row

> **NOTE**: DANGER! Forgetting the `WHERE` clause for Update or DELETE can cause every single row to update/delete.

```SQL
UPDATE dvds SET discontinued = TRUE WHERE id = 7;
```

Typically actions like creating a new item or updating and deleting an item don't return any rows.

However, sometimes we want to see the changes we made. We can add a `RETURNING` statement. This saves us from making a follow-up query if we want that data.

```SQL
UPDATE dvds SET company='Disney' WHERE id = 2 RETURNING *;
```

## Delete a Row

```SQL
DELETE FROM dvds WHERE id = 1;
```

## Delete Many Rows and See the Titles and companies

```SQL
DELETE FROM dvds WHERE discontinued = false RETURNING title, company;
```

## Limit

Our data set is very tiny right now. If we were to imagine a shopping site, we usually don't want to load hundreds or thousands of products simultaneously. It would be very slow and typically unnecessary.

We can limit how many rows we get back.

```SQL
-- select all rows from the dvd's table, but show only the first row
SELECT * FROM dvds LIMIT 1;
```

## Offset

If we were to imagine pagination for our store, we would also want to offset (start at a later row) the responses on upcoming pages.

```SQL
-- For comparison to the next one
SELECT * FROM dvds;
-- select all rows from the dvd's table, but show only one row. Skip the first row
SELECT * FROM dvds LIMIT 1 OFFSET 1;

```

## Sorting

It's essential not to rely on the order you put things in the database as a form of sorting. When you need to sort your data, do so with specific SQL commands.

```sql
-- select all rows from the dvds table, order by name alphabetically
SELECT * FROM dvds ORDER BY title ASC;

-- select all rows from the dvds table, order by name reverse alphabetically
SELECT * FROM dvds ORDER BY title DESC;

-- select all rows from the dvds table, order by price ascending
SELECT * FROM dvds ORDER BY price ASC;

-- select all rows from the dvds table, order by price descending
SELECT * FROM dvds ORDER BY price DESC;
```

## Combination

Combining statements takes a bit of thought and practice. It is also important to note that `SELECT/FROM` comes first, then `ORDER BY`, then `ASC LIMIT OFFSET`. If you try to reorganize these components, you will get a syntax error.

```SQL
SELECT title, rating, price FROM dvds ORDER BY rating, price ASC LIMIT 2 OFFSET 2;
```

## Counts and Aggregation

```sql
-- show the total number of dvds.
SELECT COUNT(price) FROM dvds;

-- divide all rows into groups by company and rating. Show the number of rows in each group. Also, show the company and rating of each group
SELECT COUNT(*) title, company, rating FROM dvds GROUP BY company, rating;

 -- Show the SUM of all the dvd prices.
SELECT SUM(price) FROM dvds WHERE price IS NOT NULL;

 -- Show the SUM of all the dvd prices where the discontinued is true
SELECT SUM(price) FROM dvds WHERE discontinued IS TRUE;

-- divide all rows into groups by whether or not they are for sale. Show the AVG of the price of each group. Also, show th property of each group
SELECT AVG(price) FROM dvds GROUP BY rating;

-- show the MIN price of dvds.
SELECT MIN(price) FROM dvds;

-- show the MAX and group them.
SELECT title, rating, MAX(price) FROM dvds GROUP BY title, rating;

```

## Quit PostgreSQL Shell

To quit `psql`, type `\q`.

### Extra Reading

A little about databases and the value null: [Hello, My Name is Mr. Null](https://www.wired.com/2015/11/null/)

If you have worked with databases before, it might be of interest to go a little bit deeper on some key features of PostgreSQL.

It is [ACID-compliant](<https://en.wikipedia.org/wiki/ACID_(computer_science)>) and [transactional](https://en.wikipedia.org/wiki/Transaction_processing)
