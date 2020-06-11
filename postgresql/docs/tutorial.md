# Chapter 1. Tutorial

PostgreSQL is an object-relational database management system (ORDBMS) based on `POSTGRES`, developed at the University of California at Berkeley Computer Science Department

PostgreSQL is an open-source descendant of this original Berkeley code

## Installation

https://www.postgresql.org/download/linux/ubuntu/

## Architectural Fundamentals

PostgreSQL uses a client/server model, session consists of the following processes:

- Server process, which manages the database files, accepts connections to database from client applications, and performs database actions on behalf of the clients. The database server program is called `postgres`.
- Client that wants to perform database operations. Client applications can be very diverse in nature: a client could be a text-oriented tool, a graphical application, a web server that accesses the database to display web pages, or a specialized database maintenance tool. Some client applications are supplied with the PostgreSQL distribution; most are developed by users.

As is typical of client/server applications, the client and the server can be on different hosts. In that case they communicate over a TCP/IP network connection. **Files that can be accessed on a client machine might not be accessibleon the database server machine.**

The PostgreSQL server can handle multiple concurrent connections from clients. To achieve this it starts (“forks”) a new process for each connection. Client and the new server process communicate without intervention by the original postgres process. **Master server process is always running, waiting for client connections, whereas client and associated server processes come and go.**

## Creating a Database

A running **PostgreSQL server can manage many databases**. Typically, a separate database is used for each project or for each user.

To create a new database, in this example named mydb, you use the following command:

`$ createdb mydb`

A convenient choice is to create a database with the same name as your current user name. Many tools assume that database name as the default, so it can save you some typing. To create that database, simply type:

`$ createdb`

**PostgreSQL user accounts are distinct from operating system user accounts**

If you do not want to use your database anymore you can remove it. For example, if you are the owner (creator) of the database mydb, you can destroy it using the following command:

`$ dropdb mydb`

## Accessing a Database (`psql`)

You probably want to start up psql to try the examples in this tutorial. It can be activated for the mydb database by typing the command:

`$ psql mydb`

**If you do not supply the database name then it will default to your user account name.**

In `psql`, you will be greeted with the following message:

```
psql (12.3)
Type "help" for help.

mydb=>
```

The last line could also be `mydb=#`

That would mean you are a database superuser, which is most likely the case if you installed the PostgreSQL instance yourself. Being a superuser means that you are not subject to access controls.

You and that you can type SQL queries into a work space maintained by psql

`SELECT version();`

The psql program has a number of internal commands that are not SQL commands. They begin with the backslash character, “\”

`mydb=> \h`

To get out of psql, type:

`mydb=> \q`

For more internal commands, type `\?` at the psql prompt.

# Chapter 2. The SQL Language

