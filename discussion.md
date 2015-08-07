---
layout: page
title: Databases and SQL  - Library edition
subtitle: Discussion
---

## Setup

For the SQL lessons, 
we will use the [SQLite](https://www.sqlite.org/) relational database management system, 
which comes pre-installed on most operating systems. 
While these instructions are specific to SQLite,
most other database management systems
(e.g. MySQL, Oracle, PostGreSQL)
have similar functions for loading data and performing basic operations.


First, download and install SQLite if it is not already installed on your operating system:

* Windows: Download the [SQLite program](http://www.sqlite.org/download.html).
* Mac OS X: <code>sqlite3</code> comes pre-installed on Mac OS X.
* Linux: <code>sqlite3</code> comes pre-installed on Linux.

Create a directory where you will carry out the exercises for this lesson, and
change to it using the <code>cd</code> command. Download the file [swclib.db](http://bit.ly/swclibdb) into this
directory.

First, load the example database into SQLite. 
On the shell command line, type

    sqlite3 swclib.db

This command instructs SQLite to load the database in the `swclib.db` file.

You should see something like the following.

    SQLite version 3.8.8 2015-01-16 12:08:06
    Enter ".help" for usage hints.
    sqlite>

For a list of useful system commands, enter <code>.help</code>.

All SQLite-specific commands are prefixed with a . to distinguish them from SQL commands. 
Type <code>.tables</code> to list the tables in the database. 

    sqlite> .tables
    Authors        Items          Works          Works_Authors


Type the following SQL <code>SELECT</code> command. 
This <code>SELECT</code> statement selects all (*) rows from the Works table.

<code>SELECT * FROM Works;</code>

Complete your SQL statement with a semicolon.

    sqlite> SELECT * FROM Works;
	1|SQL in a nutshell|9780596518844|2009|Sebastopol|O'Reilly|3rd ed.|578
	2|SQL for dummies|9781118607961|2013|Hoboken|Wiley|8th ed.|
	3|PHP & MySQL|9781449325572|2013|Sebastopol|O'Reilly|2nd ed.|532
	...

You can change some SQLite settings to make the output easier to read. 
First, 
set the output mode to display left-aligned columns. 
Then turn on the display of column headers.

    sqlite> .mode column
    sqlite> .header on
    sqlite> SELECT * FROM Works;
	Work_ID     Title              ISBN           Date        Place       Publisher   Edition     Pages     
	----------  -----------------  -------------  ----------  ----------  ----------  ----------  ----------
	1           SQL in a nutshell  9780596518844  2009        Sebastopol  O'Reilly    3rd ed.     578       
	2           SQL for dummies    9781118607961  2013        Hoboken     Wiley       8th ed.               
	3           PHP & MySQL        9781449325572  2013        Sebastopol  O'Reilly    2nd ed.     532
	...


To exit SQLite and return to the shell command line, 
you can use either `.quit` or `.exit`.


