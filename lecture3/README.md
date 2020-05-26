# Lecture 3 - SQL

## Databases
Databases can be used to make it easier for web applications to store, use, and manipulate data. Particularly useful are relational databases; in other words, tables.

SQL (Structured Query Language) is a language designed to interact with these relational databases. In this class, PostgreSQL will be used, but there are many other versions with slightly different features.

## Using SQL
When making a database or table, it is important to note what type of data will be stored in a given column. Some SQL data types are:
- `INTEGER`
- `DECIMAL`
- `SERIAL` : an automatically incrementing integer
- `VARCHAR` : variable length of characters, i.e. string
- `TIMESTAMP`
- `BOOLEAN`
- `ENUM` : one of a discrete number of possible values

In addition to a data type, columns can also have a variety of other constraints:
- `NOT NULL` : field must have a value; if field does not have a value, entry will be rejected
- `UNIQUE` : no two fields in this column can have the same value
- `PRIMARY KEY` : the main way to index a table
- `DEFAULT` : set a default value for a column if no other value is given
- `CHECK` : bound values; e.g. values greater than 50

In order to get a database running, a Postgres server must be set up. To start a server locally on a computer, use the command `psql <database>`. To connect to an online server, use `psql <databaseURL>`.

After starting up Postgres server, SQL commands can be entered directly into the terminal. Some other useful commands include:
- `\d` : print all the different parts of the current database.