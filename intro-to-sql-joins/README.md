# Introduction to SQL Joins

## Lesson Objectives

- Learn about table normalization
- Join two tables - one-to-many relationship
- Review a complex query

## Data Modeling

It's unusual to have just one table in a business application. Typically, there are many tables to represent data in a meaningful way.

Let's imagine we are designing an application for a new boutique travel travel boutique, where guests will get to work with an agent to be able to book experiences at unique hotels that are not part of any franchises. What kind of data would we need?

- Hotel
- name - string
- city - string
- state - string
- rating - integer
- pets - boolean

We can image a table like so:

|       name       |    city    |   state    | rating | pets |
| :--------------: | :--------: | :--------: | :----: | :--: |
| Hotel California |  Unknown   | California |   4    | true |
|  Great Northern  | Twin Peaks | Washington |   3    | true |

But to book a room, we would need information on the rooms available in each hotel. **One** hotel **has many** hotel rooms.

Let's try to put the info in one table:

|      name      |    city    |   state    | rating | pets |        room 1         |           room 2            |       room 3        |
| :------------: | :--------: | :--------: | :----: | :--: | :-------------------: | :-------------------------: | :-----------------: |
| Great Northern | Twin Peaks | Washington |   3    | true | queen vacant 1200 202 | penthouse 10,000 303 vacant | not vacant twin 101 |

We have a lot of data jammed into one column for each room. It's hard to read. It's easy to make a typo.

It would be better if there were a separate table for each room.

- Room
- type - string
- price - integer
- number - integer
- vacant - boolean

|   name    | price | room_num | vacant | hotel_id |
| :-------: | :---: | :------: | :----: | :------: |
|   Queen   | 1200  |   202    |  true  |    1     |
| Penthouse | 10000 |   303    |  true  |    1     |
|   Twin    |  600  |   101    | false  |    1     |

Organizing data into multiple tables to improve updating, inserting, deleting, and reading data is called [database normalization](https://en.wikipedia.org/wiki/Database_normalization).
