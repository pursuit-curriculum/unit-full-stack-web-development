# Intro to Databases/SQL Part 1

## Lesson Objectives

- Define databases and their role in a full-stack app
- Introduction to PostgreSQL
- Set up and drop (delete) a (sub) database
- Create a table, set columns, and drop the table
- **C**reate a row and put it into the table (insert data)
- **R**ead from the table (query data)
- **U**pdate from the table
- **D**elete from the table
- Bonus: Limit, Sorting, and Aggregation

## Introduction

### What is a Database?

A database is an organized collection of data stored and accessed electronically.

For our CRUD apps, we've been hard-coding some data to get started. We could make some temporary changes, but the changes would disappear as soon as we shut down or restarted the server.

We need a way to make our data persist so that we will still have the data if the database is restarted or shut down. To do this, we'll use a database to store and access our data.

There are many databases. A common type is a relational database, which stores data in tables and rows, much like an excel or google spreadsheet. We will interact with these databases using a coding language called SQL (Structured Query Language). Some are popular, like MySQL, Oracle, SQL Server, SQLite, and PostgreSQL.

While a google sheet can hold hundreds or thousands of records, it isn't meant to hold millions or billions of records (think about a database that contains all the users of FaceBook). Additionally, we can relate different tables (sheets), which allows us to do very powerful things with data. We'll get to see this in action in the next lesson.

We'll be working with PostgreSQL (a successor to a database called `ingres`), an open-source RDBMS (relational database management system) created at the University of California Berkeley. It started being built in 1982.

The [skeuomorphic representation of a database](https://en.wikipedia.org/wiki/Skeuomorph) is a stack of disks:

![](./assets/noun_database_2262303.png)

#### Extra Reading

It is [ACID-compliant](<https://en.wikipedia.org/wiki/ACID_(computer_science)>) and [transactional](https://en.wikipedia.org/wiki/Transaction_processing)
