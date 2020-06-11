# Concepts

PostgreSQL is a relational database management system (RDBMS). A system for managing data stored in relations.

Each table is a named collection of rows. Each row of a given table has the same set of named columns, and each column is of a specific data type. Whereas columns have a fixed order in each row, it is important to remember that **SQL does not guarantee the order of the rows within the table** in any way (although they can be explicitly sorted for display).

Tables are grouped into databases, and a collection of databases constitutes a database cluster.

# Creating a New Table

You can create a new table by specifying the table name, along with all column names and their types:

```sql
CREATE TABLE weather (
    city            varchar(80),
    temp_lo         int,           -- low temperature
    temp_hi         int,           -- high temperature
    prcp            real,          -- precipitation
    date            date
);
```

You can enter this into `psql`. White space (i.e., spaces, tabs, and newlines) can be used freely in SQL commands

The second example will store cities and their associated geographical location:

```sql
CREATE TABLE cities (
    name            varchar(80),
    location        point
);
```

Finally, it should be mentioned that if you don't need a table any longer or want to recreate it differently you can remove it using the following command:

`DROP TABLE tablename;`

# Populating a Table With Rows

The `INSERT` statement is used to populate a table with rows:

`INSERT INTO weather VALUES ('San Francisco', 46, 50, 0.25, '1994-11-27');`

The point type requires a coordinate pair as input, as shown here:

`INSERT INTO cities VALUES ('San Francisco', '(-194.0, 53.0)');`

The syntax used so far requires you to remember the order of the columns. An alternative syntax allows you to list the columns explicitly:

```sql
INSERT INTO weather (city, temp_lo, temp_hi, prcp, date)
    VALUES ('San Francisco', 43, 57, 0.0, '1994-11-29');
```

You can list the columns in a different order if you wish or even omit some columns

Many developers consider **explicitly listing the columns better style than relying on the order** implicitly.

Please enter all the commands shown above so you have some data to work with in the following sections.

You could also have used `COPY` to load large amounts of data from flat-text files. This is usually faster because the COPY command is optimized for this application while allowing less flexibility than `INSERT`. An example would be:

`COPY weather FROM '/home/user/weather.txt';`

where the file name for the source file must be available on the machine running the backend process, not the client.

# Querying a Table

To retrieve data from a table, the table is queried. An SQL `SELECT` statement is used to do this.

The statement is divided into:

- select list (the part that lists the columns to be returned),
- table list (the part that lists the tables from which to retrieve the data),
- optional qualification (the part that specifies any restrictions).

For example, to retrieve all the rows of table weather, type: `SELECT * FROM weather;`

Here `*` is a shorthand for “all columns”. So the same result would be had with:

`SELECT city, temp_lo, temp_hi, prcp, date FROM weather;`

You can write expressions, not just simple column references, in the select list. For example, you can do:

`SELECT city, (temp_hi+temp_lo)/2 AS temp_avg, date FROM weather;`

A query can be “qualified” by adding a `WHERE` clause that specifies which rows are wanted. The `WHERE` clause contains a Boolean (truth value) expression, and only rows for which the Boolean expression is true are returned. The usual Boolean operators (`AND`, `OR`, and `NOT`) are allowed in the qualification. For example, the following retrieves the weather of San Francisco on rainy days:

```sql
SELECT * FROM weather
    WHERE city = 'San Francisco' AND prcp > 0.0;
```

You can request that the results of a query be returned in sorted order:

```sql
SELECT * FROM weather
    ORDER BY city;

SELECT * FROM weather
    ORDER BY city, temp_lo;
```

You can request that duplicate rows be removed from the result of a query:

```sql
SELECT DISTINCT city
    FROM weather;

SELECT DISTINCT city
    FROM weather
    ORDER BY city;
```

# Joins Between Tables

...
