# MySQL Bootcamp

## Présentation du Projet

Projet créé le 30 Octobre 2020, dans le but de rafraichir ma connaissance de MySQL.

Le projet est basé sur le cours de MySQL de [Colt Steele](https://www.udemy.com/course/the-ultimate-mysql-bootcamp-go-from-sql-beginner-to-expert/)

## Database vs Database Management System

A database is a structured set of commpurterized data with an accessible interface

DBMS allows us to interface with our DB  
App -> DBMS -> DB  
MySQL, PostgreSQL,...are actually a DBMS

SQL is the language we use to "talk" to our db

## Basic CLI Commands

to get the help:

```sql
help;
```

to show the databases:

```sql
SHOW DATABASES;
```

to show user hostname:

```sql
select @@hostname;
```

## Creating Databases and tables

to create a DB (better to use snake cases):

```sql
CREATE DATABASE soap_store;
```

to delete a DB:

```sql
DROP DATABASE soap_store;
```

to use a DB:

```sql
USE soap_store;
```

to show the currently used DB:  
returns NULL if we're not currently using any db

```sql
SELECT database();
```

### Tables

tables are a collection of related data held in a structured format within a database.

tables has Columns (fields) and rows (the actual data)

#### Datatypes

3 subsets:

- Numeric Types
- String Types
- Date Types

to represent numbers, we'll work with INT; to represent text, we'll work with VARCHAR

INT represents a whole number up to ~4 billion  
VARCHAR stands for Variable-Length String, between 1 and 255 characters (CHAR is fixed length), use `varchar(15)` to specify a limit of 15 characters.

to create a table (table names should be pluralized)

```sql
CREATE TABLE tablename:
  (
    columns_name data_type,
    columns_name data_type
  );

```

eg:

```sql
CREATE TABLE cats
  (
    name VARCHAR(100),
    age INT
  );

```

to show the tables in our currently used database:

```sql
SHOW TABLES;
```

to show the columns from a table in our currently used database:

```sql
SHOW COLUMNS FROM <tablename>;
-- or
DESC <tablename>;
```

to delete a table in our currently used database:

```sql
DROP TABLE <tablename>;
```

### Challenge

1. create a _pastries_ table with 2 columns: name (max 50chars) and quantity
2. inspect the table/columns in the CLI
3. delete the table

```sql
CREATE TABLE pastries
  (
    name VARCHAR(50),
    quantity int
  );

SHOW TABLES;

DESC pastries;

DROP TABLE pastries;
```

## Inserting Data

to insert values into a table:  
the order we write the column names matters.

```sql
INSERT INTO cats(name, age)
VALUES ('Isis', 12);

```

we can bulk insert data:

```sql
INSERT INTO cats(name, age)
VALUES ('Isis', 12),
       ('Gizmo', 10),
       ('Sadie', 3);

```

### Challenge

1. create a people table with columns first_name(20 chars max), last_name(20 chars max) and age
2. insert a person, Tina Belcher, aged 13
3. check it worked
4. insert a second person, Bob Belcher, aged 42
5. insert a few more, Linda Belcher, aged 45; Phillip Frond aged 38 and Calvin Fischoeder aged 70

```sql
CREATE TABLE people(
  first_name VARCHAR(20),
  last_name VARCHAR(20),
  age INT
);

DESC people;

INSERT INTO people(first_name, last_name, age)
VALUES ("Tina", "Belcher", 13);

SELECT * FROM people;

INSERT INTO people(last_name, first_name, age)
VALUES ("Belcher", "Bob", 42);

INSERT INTO people(last_name, first_name, age)
VALUES ("Belcher", "Linda", 45),
       ("Front", "Phillip", 38),
       ("Fischoeder", "Calvin", 70);

```

### Warnings

to show warnings if eg. we enter a name that's too long (it'll get truncated automatically)

```sql
SHOW WARNINGS;
```

nb: if we screw something up eg. try to set a person's age to a string when it expects an INT, well get a wanring and the age will be set automatically to the default value

### NULL and NOT_NULL

in sql NULL means the value id not known, it does not means zero!  
NULL being set to YES (as it is by default) means that it's ok if the value for a column is null, eg. we could insert a cat with just a name in our DB even though the table has columns for name and age, the age will be automatically set to null:

```sql
INSERT INTO cats(name)
VALUES ('Alabama');
```

we could even

```sql
INSERT INTO cats()
VALUES ();
```

we can disallow that with NOT NULL:

```sql
CREATE TABLE cats2(
  name VARCHAR(100) NOT NULL,
  age INT NOT NULL
);
```

### Setting Default Values

we can set default values with DEFAULT and then the default value we want:

```sql
CREATE TABLE cats3(
  name VARCHAR(100) DEFAULT 'unnamed',
  age INT DEFAULT 1
);
```

nb: we still need to add _NOT NULL_ if we want it to not be null as we can still explicitly set a value to NULL even though we have a default value specified:

```sql
INSERT INTO cats3(name, age)
VALUES ('Montana', NULL);
```

so,

```sql
CREATE TABLE cats4(
  name VARCHAR(100) NOT NULL DEFAULT 'unnamed',
  age INT NOT NULL DEFAULT 1
);
```

### Primary Keys

without primary keys, we can insert identical records into a table, and the records wont be uniquely identifiable; we can solve this problem by adding a unique id to every record and make it the primary key

```sql
CREATE TABLE unique_cats(
  cat_id INT NOT NULL,
  name VARCHAR(100),
  age INT,
  PRIMARY KEY (cat_id)
);
```

this way we won't be able to insert another cat with the same primary key as an existing one.

we can have to unique id auto increment so we don' thave to remember the value:

```sql
CREATE TABLE unique_cats2(
  cat_id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(100),
  age INT,
  PRIMARY KEY (cat_id)
);
```

or

```sql
CREATE TABLE unique_cats2(
  cat_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  age INT
);
```

### Challenge

1. define an employee table with the following fields:

- id - number auto inc, mandatory, primary key,
- last_name - text, mandatory
- first_name - text, mandatory
- middle_name - text, not mandatory
- age - number, mandatory
- current_status - text, mandatory, defaults to "employed"

```sql
CREATE TABLE employees2(
  id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
  last_name VARCHAR(100) NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  middle_name VARCHAR(100),
  age INT NOT NULL,
  current_status VARCHAR(100) NOT NULL DEFAULT "employed"
);
```

## CRUD Operations

our starter databse:

```sql
CREATE TABLE cats
  (
     cat_id INT NOT NULL AUTO_INCREMENT,
     name   VARCHAR(100),
     breed  VARCHAR(100),
     age    INT,
     PRIMARY KEY (cat_id)
  );
DESC cats;

INSERT INTO cats(name, breed, age)
VALUES ('Ringo', 'Tabby', 4),
       ('Cindy', 'Maine Coon', 10),
       ('Dumbledore', 'Maine Coon', 11),
       ('Egg', 'Persian', 4),
       ('Misty', 'Tabby', 13),
       ('George Michael', 'Ragdoll', 9),
       ('Jackson', 'Sphynx', 7);
```

### Read

we can read data from our tables using SELECT.

```sql
SELECT * FROM cats;
```

The \* means "give me all the Columns"

if we want only the name column form the table cats:

```sql
SELECT name FROM cats;
```

if we want both name and age form the table cats:

```sql
SELECT name, age FROM cats;
```

### WHERE

the WHERE clause allows us to get specific

```sql
SELECT * FROM cats WHERE age=4;

SELECT * FROM cats WHERE name="Egg";
```

nb: by default it's case insensitive `WHERE name="Egg"` is the same as `WHERE name="EGg"`

### Challenge

1. write the SQL that selects only the cat's id
1. write the SQL that selects the cat's name and breed
1. write the SQL that selects just the Tabby cats
1. write the SQL that selects the cat's whose id is the same as their age

```sql
SELECT cat_id FROM cats;
SELECT name, breed FROM cats;
SELECT * FROM cats WHERE breed="Tabby";
SELECT * FROM cats WHERE age=cat_id;
```

### Aliases

we can specify aliases with _AS_

```sql
SELECT cat_id AS id, name FROM cats;
```

### Update

if we want to update the cat's breed from "Tabby" to "Shorthair":

```sql
UPDATE cats
SET breed="Shorthair"
WHERE breed="Tabby";
```

update Misty's age to 14:

```sql
UPDATE cats
SET age=14
WHERE name="Misty";
```

to update multiple rows:

```sql
UPDATE shirts
SET shirt_size="XS",
    color="off white"
WHERE color="white";
```

### Challenge

1. change Jackson name to Jack
1. change Ringo's breed to "British Shorthair"
1. update both Maine Coon's age to be 12

```sql
UPDATE cats
SET name="Jack"
WHERE name="Jackson";

UPDATE cats
SET breed="British Shorthair"
WHERE name="Ringo";

UPDATE cats
SET age=12
WHERE breed="Maine Coon";
```

### Delete

to delete any cats with the name Egg:

```sql
DELETE FROM cats
WHERE name="Egg";
```

to delete all the entries in the table cats:

```sql
DELETE FROM cats;
```

### Challenge

1. delete all cats that are 4 years old
1. delete cats whose age is the same as their id
1. delete all cats

```sql
DELETE FROM cats
WHERE age=4;

DELETE FROM cats
WHERE age=cat_id;

DELETE FROM cats;
```

tip: for booth update and delete, try running a select statement beofre running the update/delete statement, to make sure there are no errors as there's no undo button!
eg: run `SELECT * FROM cats WHERE age=4;` beofre running `DELETE FROM cats WHERE age=4;`

## String Functions

nb: the db is in `books.sql`

### Running SQL files

use `source path_to_file/<file_name.sql>;` to run `.sql` files from the MySQL command line, eg:

```sql
source books.sql;
```

### CONCAT

to combine multiple columns, we can use CONCAT(column, 'text', anotherColumn, 'anotherText')

```sql
SELECT CONCAT(author_fname, " ",author_lname) AS full_name FROM books;
```

to concatenate multiple fields with the same separator, we can use CONCAT_WS

```sql
SELECT CONCAT_WS("-",title, author_fname,author_lname) AS full_name FROM books;
```

### SUBSTRING

to return only part of a string we can use SUBSTRING

SQL is 1 indexed

```sql
SELECT SUBSTRING(author_fname, 1, 3) FROM books;
```

to get the last 3 letters:

```sql
SELECT SUBSTRING(author_fname, -3) FROM books;
```

SUBSTR also works as a shortcut

### REPLACE

write the SQL that returns the first 10 letters form the book's title, followed by "...", aliased as "short title"

```sql
SELECT
  CONCAT
    (
      SUBSTRING(title, 1, 10),
      "..."
    ) AS "short title"
FROM books;
```

### REPLACE

to replace part of strings, use REPLACE

```sql
SELECT REPLACE(title, 'e', '3') FROM books;
```

### REVERSE

to reverse a string, use REVERSE

```sql
SELECT REVERSE(title) FROM books;
```

### CHAR LENGTH

to count characters in strings, use CHAR_LENGTH

```sql
SELECT CHAR_LENGTH(title) FROM books;
```

### UPPER and LOWER

```sql
SELECT UPPER(author_fname), LOWER(author_lname) FROM books;
```

### Challenge

1. reverse and uppercase the following sentence "Why does my cat look at me with such hatred?"

- answer

```sql
SELECT REVERSE(UPPER("Why does my cat look at me with such hatred?"));
```

1. what does this print out?

```sql
SELECT
  REPLACE
    (
      CONCAT("I", " ", "like", " ", "cats"),
      " ",
      "-"
    ) ;
```

- answer
  "I-like-cats"

1. write the SQL that replaces spaces in book titles with '->'

- answer

```sql
SELECT
  REPLACE(title, " ", "->")
FROM books;
```

1. write the SQL that returns author's first name printed in a column titled forwards and author's first name printed backwards in a second column titled backwards

- answer

```sql
SELECT
  author_fname AS forwards,
  REVERSE(author_fname) AS backwards
FROM books;
```

1. write the SQL that returns the full authors name in caps in a column titled "full name in caps"

- answer

```sql
SELECT
  UPPER(CONCAT(author_fname," ",author_lname)) AS "full name in caps"
FROM books;
```

1. write the SQL that returns "**title** was released in **released_year**" in a column titled "blurb" for all books

- answer

```sql
SELECT
  CONCAT(title," was release in ",released_year) AS "blurb"
FROM books;
```

1. write the SQL that returns the book title and the character count for each title in a col namewrite the SQL that returnsd "character count"

- answer

```sql
SELECT
  title,
  CHAR_LENGTH(title) AS "character count"
FROM books;
```

1. write the SQL that returns "first 10 chars of title +..." as a col named "short title", "lastName,firstName" as a col named "author" and "quantity in stock" a col named as "quantity"

- answer

```sql
SELECT
  CONCAT(SUBSTRING(title, 1, 10), "...") AS "short title",
  CONCAT(author_lname,",",author_fname) AS  "author",
  CONCAT(stock_quantity," in stock") AS  "quantity"
FROM books;
```

nb: CONCAT can be used with int, it just stringify it.

## Refining Selections

### DISTINCT

use DISTINCT to ge tonly unique values

```sql
SELECT
  DISTINCT author_lname
FROM books;
```

to return distinct full names:

```sql
SELECT
  DISTINCT CONCAT(author_fname ,author_lname)
FROM books;
```

or

```sql
SELECT
  DISTINCT author_fname ,author_lname
FROM books;
```

### ORDER BY

order results with ORDER BY

```sql
SELECT
  author_lname
FROM books
ORDER BY author_lname;
```

to order in descending order, use

```sql
SELECT
  author_lname
FROM books
ORDER BY author_lname DESC;
```

the following query will order by author_fname

```sql
SELECT
  title, author_fname, author_lname
FROM books
ORDER BY 2;
```

the following query will order by author_fname and then by author_lname

```sql
SELECT
  title, author_fname, author_lname
FROM books
ORDER BY author_fname, author_lname;
```

### LIMIT

use LIMIT to specify how many results we want

```sql
SELECT
  title
FROM books
ORDER BY released_year
LIMIT 3;
```

the following will return the 3rd to 7th most recent books in the table, when there are 2 numbers after limit, the first denotes the starting index (offset)

```sql
SELECT
  title, released_year
FROM books
ORDER BY released_year DESC
LIMIT 3,5;
```

to retrieve all rows form a certain offset to the end results, we have to use a large number as 2nd parameter

```sql
SELECT
  *
FROM books
LIMIT 3,9999999;
```

### LIKE

use LIKE for better searching, as we use WHERE but not for exact matches

**%** is a wildcard character

```sql
SELECT
  *
FROM books
WHERE author_fname LIKE "%da%";
```

**\_** is also a wildcard, the following query will returns all books where the stock is at least 1000, so the **\_** wildcard specifies exactly one characters

```sql
SELECT
  title, stock_quantity
FROM books
WHERE stock_quantity LIKE "____";
```

\ is the sql escape characters

to return the books with a % in the title

```sql
SELECT
  title
FROM books
WHERE title LIKE "%\%%";
```

to return the books with a \_ in the title

```sql
SELECT
  title
FROM books
WHERE title LIKE "%\_%";
```

### Challenge

1. write the SQL that selects all the titles with "stories" in them

- answer

```sql
SELECT
  *
FROM books
WHERE title LIKE "%stories%";
```

1. write the SQL that finds the longest book

- answer

```sql
SELECT
  *
FROM books
ORDER BY pages DESC
LIMIT 1;
```

1. write the SQL that print a summary containing the "title - year" for the 3 most recent books, as a column named "summary"

- answer

```sql
SELECT
  CONCAT(title, " - ", released_year) AS summary
FROM books
ORDER BY released_year DESC
LIMIT 3;
```

1. write the SQL that selects all the titles and author's last name where the author's last name has a space in it

- answer

```sql
SELECT
  title, author_lname
FROM books
WHERE author_lname LIKE "% %";
```

1. write the SQL that finds the 3 books with the lowest stock, select the title, year and stock

- answer

```sql
SELECT
  title, released_year, stock_quantity
FROM books
ORDER BY stock_quantity
LIMIT 3;
```

1. write the SQL that returns the title and author's last name, sorted by last name and then by title

- answer

```sql
SELECT
  title, author_lname
FROM books
ORDER BY author_lname, title;
```

or

```sql
SELECT
  title, author_lname
FROM books
ORDER BY 2, 1;
```

## Aggregate Functions

### COUNT

to COUNT how many rows there are in a table, use COUNT

```sql
SELECT
  COUNT(*)
FROM books;
```

to count how many distinct authors there are in the table with

```sql
SELECT
  COUNT(DISTINCT author_lname, author_fname)
FROM books;
```

### GROUP BY

GROUP BY summarizes or aggregates identical data into single rows

the following will return one "super row" for each author_lname

```sql
SELECT
  title, author_lname
FROM books
GROUP BY author_lname;
```

so, while with the prev query we'll only see one book for each author,
we can use this to eg. count how many books each author has writen

```sql
SELECT
  author_fname, author_lname, COUNT(*)
FROM books
GROUP BY author_lname, author_fname;
```

to get a table of how many books were released every year, use

```sql
SELECT
  released_year, COUNT(*)
FROM books
GROUP BY released_year;
```

### MIN & MAX

use MIN and MAX to find the mininmum and maximum value in a table

to find the first year any books was released

```sql
SELECT
  MIN(released_year)
FROM books;
```

to find the longest book

```sql
SELECT
  MAX(pages)
FROM books;
```

to find the title of the longest book:

this doesn't work!!!

```sql
SELECT
  MAX(pages), title
FROM books;
```

we could use

```sql
SELECT
  pages, title
FROM books
ORDER BY pages DESC
LIMIT 1;
```

or a subquery,

### SUBQUERIES

we can run a query within a query

```sql
SELECT pages, title FROM books
WHERE pages = (SELECT MAX(pages)
                  FROM books);
```

### MIN & MAX with Groups By

we can use MIN and MAX like we used COUNT with GROUP BY

to find the year each author published their first book

```sql
SELECT
  author_fname, author_lname, MIN(released_year)
FROM books
GROUP BY author_fname, author_lname;
```

to find the longest page count for each author

```sql
SELECT
  author_fname, author_lname, MAX(pages)
FROM books
GROUP BY author_fname, author_lname;
```

### SUM

to add things together, use SUM

to get the sum of al lthe pages of all the books in the table

```sql
SELECT
  SUM(pages)
FROM books;
```

to sum all pages each author has written

```sql
SELECT
  author_fname, author_lname, SUM(pages)
FROM books
GROUP BY author_fname, author_lname;
```

### AVG

to average things together, use AVG  
AVG returns 4 decimal points

```sql
SELECT
  AVG(pages)
FROM books;
```

to calculate the average stock quantity for books released in the same year

```sql
SELECT
  released_year, AVG(stock_quantity)
FROM books
GROUP BY released_year;
```

### Challenge

1. write the SQL that returns the number of books in the table

- answer

```sql
SELECT
  COUNT(*)
FROM books;
```

1. write the SQL that returns how many books where released in each year

- answer

```sql
SELECT
  released_year, COUNT(*)
FROM books
GROUP BY released_year;
```

1. write the SQL that returns the total number of books in stock

- answer

```sql
SELECT
   SUM(stock_quantity)
FROM books;
```

1. write the SQL that returns the average released_year for each author

- answer

```sql
SELECT
   author_fname, author_lname, AVG(released_year)
FROM books
GROUP BY author_fname, author_lname;
```

1. write the SQL that returns the full name of the author who wrote the longest book

- answer

```sql
SELECT
   CONCAT(author_fname, " ",  author_lname) AS full_name
FROM books
ORDER BY pages DESC
LIMIT 1;
```

or

```sql
SELECT
   CONCAT(author_fname, " ", author_lname) AS full_name
FROM books
WHERE pages = (SELECT MAX(pages)
                  FROM books);
```

1. trick question:

- answer

```sql
SELECT
   released_year as "year",
   COUNT(*) AS "# books",
   AVG(pages) AS "avg pages"
FROM books
GROUP BY released_year;
```

## Data Types

### CHAR & VARCHAR

CHAR has fixed length

in the following table, if we try to insert dogs with a name loger than 5 chars it'll be truncated or cause an error if in strict mode, if we try to insert a name shorter than 5 characters, it will be right-padded with spaces, nb. when we go select the value, the spaces from the extra padding are removed!  
if we try to answer a breed longer than 10 chars, the same behavior will occur too (truncation or error)

```sql
CREATE TABLE dogs (name CHAR(5), breed VARCHAR(10));

INSERT INTO dogs (name, breed) VALUES ('bob', 'beagle');

INSERT INTO dogs (name, breed) VALUES ('robby', 'corgi');

INSERT INTO dogs (name, breed) VALUES ('Princess Jane', 'Retriever');

SELECT * FROM dogs;

INSERT INTO dogs (name, breed) VALUES ('Princess Jane', 'Retrievesadfdsafdasfsafr');

SELECT * FROM dogs;
```

CHAR is faster for fixed length text, so good to use with yes/no flags (Y/N), US states abbreviatoins (CA, NY) or sex (M/F)

### DECIMAL

DECIMAL is fixed-point type and calculations are exact

to create a decimal number we can use DECIMAL(x,y) with x as total number of digits and y as th enumber of digits after the decimal point

if we try to insert a number greater than the max, we'll an error ot it'll be rounded down, if we try to insert a number with more digits after the decimal separator than the max allowed it'll get rounded up

```sql
CREATE TABLE items(price DECIMAL(5,2));

INSERT INTO items(price) VALUES(7);

INSERT INTO items(price) VALUES(7987654);

INSERT INTO items(price) VALUES(34.88);

INSERT INTO items(price) VALUES(34.2989999);

SELECT * FROM items;
```

### FLOAT & DOUBLE

FLOAT & DOUBLE are floating-point types and calculations are approximate, but they can store large numbers using less space

DOUBLE is basically a bigger, more precise float

| DATA Type | Memory Needed | Precision Issues |
| --------- | ------------- | ---------------- |
| FLOAT     | 4 Bytes       | after ~7 digits  |
| DOUBLE    | 8 Bytes       | after ~15 digits |

```sql
CREATE TABLE thingies (price FLOAT);

INSERT INTO thingies(price) VALUES (88.45);
INSERT INTO thingies(price) VALUES (8877.45);
INSERT INTO thingies(price) VALUES (8877665544.45);

SELECT * FROM thingies;
```

more than 7 digits on the last one, so we run into precision issues

| price      |
| ---------- |
| 88.45      |
| 8877.45    |
| 8877670000 |

So which do we use? use decimal, esp. if we need the precision

### DATE, TIME & DATETIME

- DATE stores date but no time, ``YYYY-MM-DD` format
- TIME stores time but no date, ``HH:MM:SS` format
- DATETIME stores date and time, ``YYYY-MM-DD HH:MM:SS` format

```sql

CREATE TABLE people (name VARCHAR(100), birthdate DATE, birthtime TIME, birthdt DATETIME);

INSERT INTO people (name, birthdate, birthtime, birthdt)
VALUES('Padma', '1983-11-11', '10:07:35', '1983-11-11 10:07:35');

INSERT INTO people (name, birthdate, birthtime, birthdt)
VALUES('Larry', '1943-12-25', '04:10:42', '1943-12-25 04:10:42');

SELECT * FROM people;
```

| name  | birthdate  | birthtime | birthdt             |
| ----- | ---------- | --------- | ------------------- |
| Padma | 1983-11-11 | 10:07:35  | 1983-11-11 10:07:35 |
| Larry | 1943-12-25 | 04:10:42  | 1943-12-25 04:10:42 |

### CURDATE, CURTIME & NOW

- CURDATE() gives the current date
- CURTIME() gives the current time
- NOW() gives the current date and time

nb: same table as last section

```sql
INSERT INTO people (name, birthdate, birthtime, birthdt)
VALUES('Toaster', CURDATE(), CURTIME(), NOW());

SELECT * FROM people;
```

| name    | birthdate  | birthtime | birthdt             |
| ------- | ---------- | --------- | ------------------- |
| Padma   | 1983-11-11 | 10:07:35  | 1983-11-11 10:07:35 |
| Larry   | 1943-12-25 | 04:10:42  | 1943-12-25 04:10:42 |
| Toaster | 2020-11-05 | 13:40:52  | 2020-11-05 13:40:52 |

### Formatting Dates

get the day, the name of the day, th eday of the week, the day of the year, the month, the name of the month from a DATE or DATETIME

```sql
SELECT name, DAY(birthdate) FROM people;
SELECT name, birthdate, DAY(birthdate) FROM people;
SELECT name, birthdate, DAYNAME(birthdate) FROM people;
SELECT name, birthdate, DAYOFWEEK(birthdate) FROM people;
SELECT name, birthdate, DAYOFYEAR(birthdate) FROM people;
SELECT name, birthdt, MONTH(birthdt) FROM people;
SELECT name, birthdt, MONTHNAME(birthdt) FROM people;
```

get the hour, the minute for a TIME or DATETIME data type

```sql
SELECT name, birthtime, HOUR(birthtime) FROM people;
SELECT name, birthtime, MINUTE(birthtime) FROM people;
```

use DATE_FORMAT to format a date nicely with specifiers

```sql
SELECT DATE_FORMAT(birthdt, 'Was born on a %W') FROM people;
SELECT DATE_FORMAT(birthdt, '%m/%d/%Y') FROM people;
SELECT DATE_FORMAT(birthdt, '%m/%d/%Y at %h:%i') FROM people;
```

### Date Math

to get the number of days between two dates, use DATEDIFF (also accepts DATETIME)

```sql
SELECT name, birthdate, DATEDIFF(NOW(), birthdate) FROM people;
```

to add dates, use DATE_ADD, tu substract dates, use DATE_SUB

```sql
SELECT birthdt, DATE_ADD(birthdt, INTERVAL 1 MONTH) FROM people;
SELECT birthdt, DATE_ADD(birthdt, INTERVAL 10 SECOND) FROM people;
SELECT birthdt, DATE_ADD(birthdt, INTERVAL 3 QUARTER) FROM people;
```

we can also use + and -

```sql
SELECT birthdt, birthdt + INTERVAL 1 MONTH FROM people;
SELECT birthdt, birthdt - INTERVAL 5 MONTH FROM people;
SELECT birthdt, birthdt + INTERVAL 15 MONTH + INTERVAL 10 HOUR FROM people;
```

### TIMESTAMPS

TIMESTAMP is also a datatype, it stores date and time like DATETIME but the range is smaller (from 1970 to 2038) and it takes half the storage space

typically, use DATETIME for everything, except this

```sql
CREATE TABLE comments (
    content VARCHAR(100),
    created_at TIMESTAMP DEFAULT NOW()
);

INSERT INTO comments (content) VALUES('lol what a funny article');
INSERT INTO comments (content) VALUES('I found this offensive');
INSERT INTO comments (content) VALUES('Ifasfsadfsadfsad');

SELECT * FROM comments ORDER BY created_at DESC;
```

changed_at stores a time stamp and is updated each time the row is changed

```sql
CREATE TABLE comments2 (
    content VARCHAR(100),
    changed_at TIMESTAMP DEFAULT NOW() ON UPDATE NOW()
);

INSERT INTO comments2 (content) VALUES('dasdasdasd');
INSERT INTO comments2 (content) VALUES('lololololo');
INSERT INTO comments2 (content) VALUES('I LIKE CATS AND DOGS');

UPDATE comments2 SET content='THIS IS NOT GIBBERISH' WHERE content='dasdasdasd';

SELECT * FROM comments2 ORDER BY changed_at;
```

### Challenge

1. fill in the blanks, price always < 1 million

```sql
CREATE TABLE inventory(
  item_name ____,
  price ____,
  quantity ____
);
```

- answer

```sql
CREATE TABLE inventory(
  item_name VARCHAR(100),
  price DECIMAL(8,2),
  quantity INT
);
```

1. print out the current time, date and day of the week (the nr), the name of the day of the week

- answer

```sql
SELECT CURDATE();
SELECT CURTIME();
SELECT DAYOFWEEK(NOW()), DAYNAME(NOW());
```

1. print out the current day and time using the format mm/dd/yyyy

- answer

```sql
SELECT DATE_FORMAT(NOW(), '%m/%d/%Y');
```

or CURDATE() instead of NOW()

1. print out the current day and time using the format "April 1st at 10:18"

- answer

```sql
SELECT DATE_FORMAT(NOW(), '%M %D at %h:%i');
```

1. create a tweets table that stores the tweet content, the username, the time it was created

- answer

```sql
CREATE TABLE tweets(
  tweet VARCHAR(100),
  username VARCHAR(20),
  created_at TIMESTAMP DEFAULT NOW()
),
```

## Logical Operators

### Not Equal

to get all the books where the released year isn't 2017

```sql
SELECT
  title, released_year
FROM books
WHERE released_year != 2017;
```

### Not Like

to get all the books where the author's last name doesn't start with a "G"

```sql
SELECT
  title, author_lname
FROM books
WHERE author_lname NOT LIKE "G%";
```

### Greater Than

to get the title and page count for the books with more than 300 pages

```sql
SELECT
  title, pages
FROM books
WHERE pages > 300;
```

greather than equal or equal to also exists, >=

nb:

```sql
SELECT "a" > "b";
SELECT "A" > "a";
SELECT "A" < "a";
```

all return false ("A"="a" for MySQL; and "b">"a")

### Less Than

to get the title and released year for all books released before 2015

```sql
SELECT
  title, released_year
FROM books
WHERE released_year < 2015;
```

less than equal or equal to also exists, <=

### AND

&&/AND is the logical AND

to select books written by Dave Eggers published after the year 2010

```sql
SELECT
  *
FROM books
WHERE released_year > 2010 AND author_lname = "Eggers" AND author_fname = "Dave";
```

### OR

||/OR is the logical OR

to select books written by Eggers or published after the year 2010

```sql
SELECT
  *
FROM books
WHERE released_year > 2010 || author_lname = "Eggers";
```

### Between

to select things based of a lower and upper range, we use BETWEEN
nb: BETWEEN is inclusive of its two bounds!!

to select all books published between 2001 and 2017 inclusive

```sql
SELECT
  *
FROM books
WHERE released_year BETWEEN 2001 AND 2017;
```

it's the same as

```sql
SELECT
  *
FROM books
WHERE released_year >= 2001 && released_year <= 2017;
```

NOT BETWEEN is also a thing:

to select all books not published between 2001 and 2017 inclusive

```sql
SELECT
  *
FROM books
WHERE released_year NOT BETWEEN 2001 AND 2017;
```

#### CAST()

to explicitely convert from one datatype to another, use CAST()  
to compare a DATETIME to a DATE value, we should cast the DATE value to a DATETIME value first  
to convert the string "2017-05-02" to a DATETIME, use

```sql
SELECT CAST("2017-05-02" AS DATETIME);
```

### IN & NOT IN

to check if a value is in a specific set of values, use IN

to select all the books written by Carver, Lahiri or Smith, use

```sql
SELECT
  *
FROM books
WHERE author_lname IN ("Carver", "Lahiri", "Smith");
```

we could also use multiple OR statements

to select all the books not written by Carver, Lahiri or Smith, use

```sql
SELECT
  *
FROM books
WHERE author_lname NOT IN ("Carver", "Lahiri", "Smith");
```

### Case Statements

we can write case statements in MySQL with CASE..END

to return the title, released_year and a "genre" column where the genre is 'Modern Lit' for books written from 2000 and '20th Century Lit' for books written before

```sql
SELECT title, released_year,
       CASE
         WHEN released_year >= 2000 THEN 'Modern Lit'
         ELSE '20th Century Lit'
       END AS GENRE
FROM books;
```

to return the title, stock_quantity and a "stock" column where the stock is one star for book with 50 or less stock, 2 stars for books with 51 to 100 stock and 3 stars for books with more stock

```sql
SELECT title, stock_quantity,
    CASE
        WHEN stock_quantity BETWEEN 0 AND 50 THEN '*'
        WHEN stock_quantity BETWEEN 51 AND 100 THEN '**'
        ELSE '***'
    END AS STOCK
FROM books;
```

or
(takes advantage of order of execution)

```sql
SELECT title, stock_quantity,
    CASE
        WHEN stock_quantity <= 50 THEN '*'
        WHEN stock_quantity <= 100 THEN '**'
        ELSE '***'
    END AS STOCK
FROM books;
```

### Challenge

1. write the SQL that returns all books written by eggers or chabon

- answer

```sql
SELECT
  title, author_lname
FROM books
WHERE author_lname in ('Eggers', 'Chabon');
```

1. write the SQL that returns all book swritten by Lahiri published after 2000

- answer

```sql
SELECT
  title, author_lname
FROM books
WHERE author_lname = "Lahiri" AND released_year > 2000;
```

1. write the SQL that returns all books with page counts between 100 and 200

- answer

```sql
SELECT
  title
FROM books
WHERE pages BETWEEN 100 AND 200;
```

1. write the SQL that returns all books where the author_lname starts with "C" or "S"

- answer

```sql
SELECT
  title, author_lname
FROM books
WHERE author_lname LIKE "C%" OR author_lname LIKE "S%";
```

or

```sql
SELECT
  title, author_lname
FROM books
WHERE SUBSTR(author_lname,1,1) IN("C", "S");
```

1. write the SQL that returns the title, the author_lname and a "type" column where type is "Short Stories" if the title contains "stories", "Memoir" if the title contains "Just Kids" or "A heartbreaking Work", and "Novel" for everything else

- answer

```sql
SELECT
  title,
  author_lname,
  CASE
      WHEN title LIKE "%stories%" THEN "Short Stories"
      WHEN title LIKE "%A Heartbreaking Work%" 
        OR title LIKE "%Just Kids%" THEN "Memoir"
      ELSE "Novel"
      END AS "type"
FROM books;
```
