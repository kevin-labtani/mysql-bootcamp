# MySQL Bootcamp

## Présentation du Projet

Projet créé le 30 Octobre 2020, dans le but de rafraichir ma connaissance de MySQL.

Le projet est basé sur le cours de MySQL de [Colt Steele](https://www.udemy.com/course/the-ultimate-mysql-bootcamp-go-from-sql-beginner-to-expert/)

## Contributeurs

- [**Kevin Labtani**](https://github.com/kevin-labtani)

## Langages et technologies

- MariaDB

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

tables has Columns (headers) and rows (the actual data)

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

### Challenge:

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
