# Many-to-Many Joins

By the end of this lesson, you should be able to:

- Explain how to create and use a many-to-many relationship between two entities.
- Explain what a lookup table is and what function it serves

## Intro

Next up, we can create tables with relationships of many-to-many. For example, a guest can have (book) many hotel rooms and a hotel room can have (be booked by) many guests.

Let's make some guests. We imagine we'd have information like a first name, last name, credit card number, email, phone, etc. But to keep things simple for this demonstration, our guests will only have one column for their name and whether or not they are traveling with a pet.

Create table:

```SQL
CREATE TABLE guests (id SERIAL PRIMARY KEY, name TEXT, pet BOOLEAN);
```

Insert data:

```SQL
INSERT INTO guests (name, pet)
VALUES
('Winter Jones', true),
('Forest Doe', false),
('River Miller', true);
```

Confirm correct table and insertion of data:

```SQL
SELECT * FROM guests;
```

- How can we create a table that allows for this relationship?

- What is a lookup (or join) table?
- What is the purpose of a lookup table?
- What kind of values can a lookup table have?
- Does a lookup table require a unique id field? Why or why not?

Create table:

```SQL
CREATE TABLE rooms_guests (room_id INT, guest_id INT, stay_dates TEXT);
```

Insert data:

```SQL
INSERT INTO rooms_guests (room_id, guest_id, stay_dates)
VALUES
(1,1, 'June 6-12'),
(4,2, 'July 6-12'),
(3,3, 'August 6-12'),
(4,1, 'September 6-12'),
(6,2, 'October 6-12'),
(7,3, 'November 6-12'),
(8,2, 'December 6-12'),
(2,3, 'January 6-12'),
(1,1, 'February 6-12'),
(1,1, 'March 6-12'),
(2,1, 'April 6-12'),
(3,1, 'June 22-28'),
(4,1, 'July 19-22');
```

Confirm correct table and insertion of data:

```SQL
SELECT * FROM rooms_guests;
```

While this table has data, it's not very useful for us to look at. Joining all three tables will provide a useful view of the data.

Select all columns (all tables) from rooms_guests, join the table guests on where guests.id matches rooms_guests.guest_id match.

Join those results to rooms_guests on where rooms.id rooms_guests.room_id match.

```SQL
SELECT
 *
FROM
 rooms_guests
JOIN
 guests
ON
 guests.id = rooms_guests.guest_id
JOIN
 rooms
ON
 rooms.id = rooms_guests.room_id;

```

![](../assets/join-guests-rooms.png)

## Bonus

- Show which hotels are associated with these rooms and update the three fields `name` to `guest_name`, `hotel_name`, and `room_name`.

- Additionally, show stay dates, price, and state and order by guest name.

**Expected Result**

![](../assets/bonus-result.png)
