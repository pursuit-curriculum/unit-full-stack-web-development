# Introduction to SQL Joins

## Lesson Objectives

By the end of this lesson, you should be able to:

- Explain the purpose of table normalization.
- Describe the business and technical implications of a one-to-many relationship.
- Explain the function of a foreign key.
- Implement a join on two tables through matching IDs
- Explain how to create and use a many-to-many relationship between two entities.
- Explain what a lookup table is and what function it serves

## Data modeling and normalization

It's unusual to have just one table in a business application. Typically, there are many tables to represent data in a meaningful way.

Let's imagine you are designing an application for a new boutique travel travel boutique, where guests will get to work with an agent to be able to book experiences at unique hotels that are not part of any franchises. What kind of data would you need?

- Hotel
  - name - string
  - city - string
  - state - string
  - rating - integer
  - pets - boolean

You can image a table like so:

|       name       |    city    |   state    | rating | pets |
| :--------------: | :--------: | :--------: | :----: | :--: |
| Hotel California |  Unknown   | California |   4    | true |
|  Great Northern  | Twin Peaks | Washington |   3    | true |

But to book a room, you would need information on the rooms available in each hotel. **One** hotel **has many** hotel rooms.

Let's try to put the info in one table:

|      name      |    city    |   state    | rating | pets |        room 1         |           room 2            |       room 3        |
| :------------: | :--------: | :--------: | :----: | :--: | :-------------------: | :-------------------------: | :-----------------: |
| Great Northern | Twin Peaks | Washington |   3    | true | queen vacant 1200 202 | penthouse 10,000 303 vacant | not vacant twin 101 |

You have a lot of data jammed into one column for each room. It's hard to read. It's hard to update. It is hard to make a query for which rooms are vacant.

It would be better if there were a separate table for each room.

- Room
  - type - string
  - price - integer
  - number - integer
  - vacant - boolean

|   name    | price | room_num | vacant |
| :-------: | :---: | :------: | :----: |
|   Queen   | 1200  |   202    |  true  |
| Penthouse | 10000 |   303    |  true  |
|   Twin    |  600  |   101    | false  |

Organizing data into multiple tables to improve updating, inserting, deleting, and reading data is called [database normalization](https://en.wikipedia.org/wiki/Database_normalization).

## Joins

### Relating tables and foreign keys

Now that hotels and their rooms are two separate tables, how can you relate the two tables back to each other?

First, the hotels must have a unique identifying field. In this case a simple integer will be used:

| id  |       name       |    city    |   state    | rating | pets |
| :-: | :--------------: | :--------: | :--------: | :----: | :--: |
|  1  | Hotel California |  Unknown   | California |   4    | true |
|  2  |  Great Northern  | Twin Peaks | Washington |   3    | true |

Next, the rooms will also have a unique id:

| id  |   name    | price | room_num | vacant |
| :-: | :-------: | :---: | :------: | :----: |
| 10  |   Queen   | 1200  |   202    |  true  |
| 11  | Penthouse | 10000 |   303    |  true  |
| 12  |   Twin    |  600  |   101    | false  |

Let's say all these rooms happen to belong to The Great Northern or Hotel California. What you can do is include the hotel id as a field on the hotel rooms.

| id  |   name    | price | room_num | vacant | hotel_id |
| :-: | :-------: | :---: | :------: | :----: | :------: |
| 10  |   Queen   | 1200  |   202    |  true  |    2     |
| 11  | Penthouse | 10000 |   303    |  true  |    1     |
| 12  |   Twin    |  600  |   101    | false  |    2     |

This would allow you to create a query to list all the rooms associated with The Great Northern.

```SQL
SELECT * FROM rooms WHERE hotel_id = 2;
```

The id field of the hotel in the rooms table is called a `foreign key` because it is the unique identifier that belongs to the hotel in another table (a foreign table, if you will).

### Joining tables

Imagine you would like to know which hotels have vacancies. You would need to query the rooms table and find where vacant is true. Then, you would need to look up the hotels that match. To perform this query, you need information from both tables. You can join the data in SQL using a `join` statement.

There are four main ways to join tables. Before listing them out it is important to be aware there are four possibilities. Additionally, when you put two tables next to each other one will be on the left and one will be on the right, therefore there are references to `left` and `right` when describing joins.

In the following examples, hotels is the left table, and rooms are the right table:

- Listing all the hotels and room data. (full join)
- Listing all the hotel data and only the room data that has an associated hotel.(left join)
- Listing only the hotel data that has at least one associated room.(right join)
- Listing only the hotels and rooms that have associations. (inner join)

Here is a simplified visual of the four types of joins.

![](./assets/sql-join-venn-diagram.png)

**Caveat:** Some people don't like using Venn Diagrams to describe joins as it is an oversimplified representation of what is happening. That's ok! Venn Diagrams are a great way to get started. When you are ready, you can read something more technical like [this](https://blog.jooq.org/2016/07/05/say-no-to-venn-diagrams-when-explaining-joins/)

#### Full Outer Join

Start with a full join. That will put all of the data of both tables together.You will join the two tables based on when the hotel `id` from the `hotels` table matches the `hotel_id` of the `rooms` table.

This will show you hotels with no room data (Grand Budapest Hotel) and 3 rooms with a hotel_id of 7, even though there is no hotel with an id of 7.

```sql
SELECT * FROM hotels FULL OUTER JOIN rooms ON hotels.id = rooms.hotel_id;
```

You can see that you get a new (temporary) table that shows all your hotels data on the left and your rooms data on the right. The rows are aligned based on the value of the hotel id from the hotels table and the `foreign key` from the rooms table.

![](./assets/full-hotel-room.png)

Determining which table is on the left or right is based on the order you write your SQL statement.

```sql
SELECT * FROM rooms FULL OUTER JOIN hotels ON hotels.id = rooms.hotel_id;
```

![](./assets/full-room-hotel.png)

**Note:** Total of 17 rows

Due to the simplicity of your work, there is no compelling reason to put your hotels or rooms on either side besides personal choice. As you learn to build more complex queries where you may be joining 3 or more tables, there would be a need for more careful thought about the order of how the tables are joined.

#### Inner Join

A lot of times, you want to avoid looking at data with null values. You'd use an inner join if we're going to ignore all the data with no matching values from both tables.

Let's try it.

```sql
SELECT * FROM hotels INNER JOIN rooms ON hotels.id = rooms.hotel_id;
```

![](./assets/inner-hotel-room.png)

**Note:** Total of 13 rows

You can still have null values within your tables. What is excluded is `Grand Budapest Hotel` because there are no hotel rooms that go with it, and there are no rooms with a foreign key `hotel_id` of `7` because there is no hotel in your database with an `id` of `7`.

#### Left Join

If you still want to see the Grand Budapest Hotel, even though there are no rooms associated with it. You can do a left join.

```sql
SELECT * FROM hotels LEFT JOIN rooms ON hotels.id = rooms.hotel_id;
```

![](./assets/left-hotel-room.png)

**Note:** Total of 14 rows

Notice that in this table, you have 14 rows (+ 1 for Grand Budapest Hotel), whereas, in your previous inner join, you had 13 rows.

#### Right Join

If you do the right join with hotels and rooms, can you hypothesize how many rows you should have?

```sql
SELECT * FROM hotels RIGHT JOIN rooms ON hotels.id = rooms.hotel_id;
```

![](./assets/right-hotel-room.png)

**Note:** Total of 16 rows

Now you see that the Grand Budapest Hotel is excluded, but those mystery 3 rooms with no hotel are now showing up.

## Many-to-many joins

### Look up table
