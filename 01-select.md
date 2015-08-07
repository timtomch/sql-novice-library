---
layout: page
title: Databases and SQL - Library edition
subtitle: Selecting Data
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Explain the difference between a table, a record, and a field.
> *   Explain the difference between a database and a database manager.
> *   Write a query to select all values for specific fields from a single table.

A [relational database](reference.html#relational-database)
is a way to store and manipulate information. 
Databases are arranged as [tables](reference.html#table).
Each table has columns (also known as [fields](reference.html#field)) that describe the data,
and rows (also known as [records](reference.html#record)) which contain the data.

When we are using a spreadsheet,
we put formulas into cells to calculate new values based on old ones.
When we are using a database,
we send commands
(usually called [queries](reference.html#query))
to a [database manager](reference.html#database-manager):
a program that manipulates the database for us.
The database manager does whatever lookups and calculations the query specifies,
returning the results in a tabular form
that we can then use as a starting point for further queries.

> ## Changing database managers {.callout}
>
> Every database manager --- Oracle,
> IBM DB2, PostgreSQL, MySQL, Microsoft Access, and SQLite --- stores
> data in a different way,
> so a database created with one cannot be used directly by another.
> However,
> every database manager can import and export data in a variety of formats, like .csv,
> so it *is* possible to move information from one to another.

Queries are written in a language called [SQL](reference.html#sql),
which stands for "Structured Query Language".
SQL provides hundreds of different ways to analyze and recombine data.
We will only look at a handful of queries,
but that handful accounts for most of what scientists do.

The tables below show the database we will use in our examples:

> **Works**: table containing bibliographic details for the library's books
>
> Work_ID     Title              ISBN           Date        Place       Publisher   Edition     Pages     
> ----------  -----------------  -------------  ----------  ----------  ----------  ----------  ----------
> 1           SQL in a nutshell  9780596518844  2009        Sebastopol  O'Reilly    3rd ed.     578       
> 2           SQL for dummies    9781118607961  2013        Hoboken     Wiley       8th ed.     -null-          
> 3           PHP & MySQL        9781449325572  2013        Sebastopol  O'Reilly    2nd ed.     532       
> 4           Using SQLite       9780596521189  2010        Sebastopol  O'Reilly    1st ed.     503       
> 5           Geek sublime       9780571310302  2014        London      Faber & Fa  -null-      258       
> 6           Capital in the 21  9780674430006  2014        Cambridge   Belknap Pr  -null-      685       
> 7           SQL                9780071548649  2009        New York    McGraw-Hil  3rd ed.     534       
> ...

> **Items**: table containing details on copies of Works owned by the library
>
> Item_ID     Work_ID     Barcode       Acquired    Status    
> ----------  ----------  ------------  ----------  ----------
> 1           1           081722942611  2009        Loaned    
> 2           1           492437609065  2011        On shelf  
> 3           2           172480710952  2013        On shelf  
> 4           3           708014968732  2013        Missing   
> ...

> **Authors**: table containing details on authors and contributors 
>
> Author_ID   Family      Personal    Occupation  Birth       Death     
> ----------  ----------  ----------  ----------  ----------  ----------
> 1           Kline       Kevin E.    -null-      1966        -null-          
> 2           Kline       Daniel      -null-      -null-      -null-                
> 3           Hunt        Brand       -null-      -null-      -null-                
> 4           Taylor      Allen G.    -null-      1945        -null-          
> ...

> **Works_Authors**: table linking works with authors
>
> Work_ID     Author_ID   Role      
> ----------  ----------  -----------
> 1           1           Author    
> 1           2           Contributor
> 1           3           Contributor
> 2           4           Author    
> ...

Notice that several entries don't contain any actual
data, but instead have a special `-null-` entry:
we'll return to these missing values [later](05-null.html).
For now,
let's write an SQL query that displays authors' names.
We do this using the SQL command `SELECT`,
giving it the names of the columns we want and the table we want them from.
Our query and its output look like this:

~~~ {.sql}
SELECT family, personal FROM Authors;
~~~

Family      Personal  
----------  ----------
Kline       Kevin E.  
Kline       Daniel    
Hunt        Brand
...   

The semicolon at the end of the query
tells the database manager that the query is complete and ready to run.
We have written our commands and column names in lower case,
and the table name in Title Case,
but we don't have to:
as the example below shows,
SQL is [case insensitive](reference.html#case-insensitive).

~~~ {.sql}
SeLeCt FaMiLy, PeRsOnAl FrOm wORks;
~~~

Family      Personal  
----------  ----------
Kline       Kevin E.  
Kline       Daniel    
Hunt        Brand
... 

You can use SQL's case insensitivity to your advantage. For instance, some people choose to write SQL keywords (such as `SELECT` and `FROM`) in capital letters and **field** and **table** names in lower case. This can make it easier to locate parts of an SQL statement. For instance, you can scan the statement, quickly locate the prominent `FROM` keyword and know the table name follows.
Whatever casing convention you choose,
please be consistent:
complex queries are hard enough to read without the extra cognitive load of random capitalization.
One convention is to use UPPER CASE for SQL statements, to distinguish them from tables and column
names. This is the convention that we will use for this lesson.

Going back to our query,
it's important to understand that
the rows and columns in a database table aren't actually stored in any particular order.
They will always be *displayed* in some order,
but we can control that in various ways.
For example,
we could swap the columns in the output by writing our query as:

~~~ {.sql}
SELECT personal, family FROM Authors;
~~~

Personal    Family    
----------  ----------
Kevin E.    Kline     
Daniel      Kline     
Brand       Hunt      
Allen G.    Taylor   
...

or even repeat columns:

~~~ {.sql}
SELECT personal, personal FROM Authors;
~~~

Personal    Personal  
----------  ----------
Kevin E.    Kevin E.  
Daniel      Daniel    
Brand       Brand     
Allen G.    Allen G.
...  

As a shortcut,
we can select all of the columns in a table using `*`:

~~~ {.sql}
SELECT * FROM Authors;
~~~

Author_ID   Family      Personal    Occupation  Birth       Death     
----------  ----------  ----------  ----------  ----------  ----------
1           Kline       Kevin E.                1966                  
2           Kline       Daniel                                        
3           Hunt        Brand                                         
4           Taylor      Allen G.                1945                  
...

> ## Selecting Work Titles {.challenge}
>
> Write a query that selects only titles from the `Works` table.

> ## Query Style {.challenge}
>
> Many people format queries as:
>
> ~~~
> SELECT personal, family FROM authors;
> ~~~
>
> or as:
>
> ~~~
> select Personal, Family from AUTHORS;
> ~~~
>
> What style do you find easiest to read, and why?