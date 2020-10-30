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
