---
layout: page
title: Databases and SQL - Library edition
subtitle: Creating and Modifying Data
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Write statements that creates tables.
> *   Write statements to insert, modify, and delete records.

So far we have only looked at how to get information out of a database,
both because that is more frequent than adding information,
and because most other operations only make sense
once queries are understood.
If we want to create and modify data,
we need to know two other sets of commands.

The first pair are [`CREATE TABLE`][CREATE-TABLE] and [`DROP TABLE`][DROP-TABLE].
While they are written as two words,
they are actually single commands.
The first one creates a new table;
its arguments are the names and types of the table's columns.
For example,
the following statements create the four tables in our library database:

~~~ {.sql}
CREATE TABLE Authors(Author_ID integer, Family text, Personal text, Occupation text, Birth integer, Death integer);
CREATE TABLE Items(Item_ID integer, Work_ID integer, Barcode text, Acquired integer, Status text);
CREATE TABLE Works(Work_ID integer, Title text, ISBN text, Date integer, Place text, Publisher text, Edition text, Pages integer);
CREATE TABLE Works_Authors(Work_ID integer, Author_ID integer, Role text);
~~~

We can get rid of one of our tables using:

~~~ {.sql}
DROP TABLE Items;
~~~

Be very careful when doing this:
most databases have some support for undoing changes,
but it's better not to have to rely on it.

Different database systems support different data types for table columns,
but most provide the following:

data type  use
---------  -----------------------------------------
INTEGER    a signed integer
REAL       a floating point number
TEXT       a character string
BLOB       a "binary large object", such as an image

Most databases also support Booleans and date/time values;
SQLite uses the integers 0 and 1 for the former,
and represents the latter as discussed [earlier](#a:dates).
An increasing number of databases also support geographic data types,
such as latitude and longitude.
Keeping track of what particular systems do or do not offer,
and what names they give different data types,
is an unending portability headache.

When we create a table,
we can specify several kinds of constraints on its columns.
For example,
a better definition for the `Items` table would be:

~~~ {.sql}
CREATE TABLE Items(
	Item_ID integer not null,   -- all items should have an ID 
	Work_ID integer not null,   -- all items should be linked to a title
	Barcode text,               -- all other fields can be NULL
	Acquired integer, 
	Status text,
	primary key(Item_ID),
	foreign key(Work_ID) references Works(Work_ID)
);
~~~

Once again,
exactly what constraints are available
and what they're called
depends on which database manager we are using.

Once tables have been created,
we can add, change, and remove records using our other set of commands,
`INSERT`, `UPDATE`, and `DELETE`.

The simplest form of `INSERT` statement lists values in order:

~~~ {.sql}
INSERT INTO authors VALUES(25,'Shakespeare', 'William', 'Playwright', NULL, 1616);
~~~

If we are unsure in which order the columns were defined when the table was created,
we can also specify in which column each value should be stored:

~~~ {.sql}
INSERT INTO authors(Family, Personal, Birth) VALUES('Atwood', 'Margaret', 1939);
~~~

We can also insert values into one table directly from another:

~~~ {.sql}
CREATE TABLE Author_names(family text, personal text);
INSERT INTO Author_names SELECT family, personal FROM Authors;
~~~

Modifying existing records is done using the `UPDATE` statement.
To do this we tell the database which table we want to update,
what we want to change the values to for any or all of the columns,
and under what conditions we should update the values.

For example, if we wanted to update one of the records from the
`Works` table where the publication date is missing, we would write

~~~ {.sql}
UPDATE Works SET Date=2006 WHERE Work_ID=20;
~~~

While we are at it, we could also have added other information that was
missing, by editing multiple columns in one statement:

~~~ {.sql}
UPDATE Works SET Date=2005, Edition='1st' WHERE Work_ID=20;
~~~

Be careful to not forget the `WHERE` clause or the `UPDATE` statements will
modify *all* of the records in the database!

Deleting records can be a bit trickier,
because we have to ensure that the database remains internally consistent.
If all we care about is a single table,
we can use the `DELETE` command with a `WHERE` clause
that matches the records we want to discard.
For example,
once we realize that our library doesn't include
titles written by Margaret Atwood,
we could remove her from the `Authors` table like this:

~~~ {.sql}
DELETE FROM Authors WHERE family='Atwood';
~~~

But what if we removed another author, such as Kevin E. Kline (Author_ID = 1) instead?
Our `Works` table would still contain three records
of titles to which he is a contributor. 
Remember, the relationship between
`Works` and `Authors` is stored in the `Works_Authors` table:

~~~ {.sql}
SELECT * FROM Works_Authors WHERE Author_ID = 1;
~~~

|Work_ID   |Author_ID |Role       |
|----------|----------|-----------|
|1         |1         |Author     |
|16        |1         |Contributor|
|19        |1         |Author     |

This problem is called [referential integrity](reference.html#referential-integrity):
we need to ensure that all references between tables can always be resolved correctly.
If we are weeding our collection and really want to delete all records of titles
authored by Kevin Kline, we would not only need to remove him from the `Authors`
table, but also remove the rows in the `Works_Authors` table that refer to him,
as well as remove all the works that he authored from the `Works` table.

If our database manager supports it,
we could automate this
using [cascading delete](reference.html#cascading-delete).
However,
this technique is outside the scope of this chapter.

Also, it is often better to identify records as "deleted" without actually deleting
them. In our example, instead of deleting all the information pertaining to the
David Kline titles, we could change the `Status` column in the corresponding rows
in the `Items` table to e.g. `Deleted`.

> ## Hybrid Storage Models {.callout}
>
> Many applications use a hybrid storage model
> instead of putting everything into a database:
> the actual data (such as scanned images from a digital library) is stored in files,
> while the database stores the files' metadata, such as bibliographic information,
> file type, date scanned, image source
> and so on.

> ## Extracting Publisher Data {.challenge}
>
> Instead of storing the `Publisher` information in the `Works` table, we
> could create an authority file for publishers by storing this information
> in a separate table, then linking it to the `Works` table, much like the
> authors are managed.
>
> The first step would be to retrieve the existing information on publishers
> from the `Works` table. Create a table that contains only publisher names 
> from the `Works` table. Warning: avoid duplicates!


> ## Replacing  a NULL  value {.challenge}
>
> The number of pages for 'SQL for dummies', 8th edition is 480. Update the Works table accordingly.


> ## Replacing all NULL values {.challenge}
>
> In the `Authors` table, NULL can either mean that a value was not
> documented (we don't know this information), or that an author is
> contemporary (the `death` column is empty).
> Write a statement that replaces the NULL value in the column
> `death` with the 
> string 'ACTIVE' for authors where only the birth date is known.
> Warning: do not replace all NULL values in that column!

> ## Generating Insert Statements {.challenge}
>
> The collection from another library is being integrated in our
> database. The bibliograhpic information for the titles in that collection
> was extracted from another database, and is now available as a
> [CSV](reference.html#comma-separated-values) file, which is formatted like
> this:
>
> ~~~ {.output}
> Title,Authors,ISBN,Publication
> "Joe Celko's SQL for Smarties : Advanced SQL Programming","Celko, Joe","Burlington : Elsevier Science, 2015"
> "SQL queries for mere mortals : a hands-on guide to data manipulation in SQL", "Viescas, John L;Hernandez, Michael J", "Harlow : Addison-Wesley, 2014."
> ~~~
>
> Write a small Python program that reads this file in and prints out
> the SQL `INSERT` statements needed to add these records to the
> library database.
> Note, you will not only need to issue statements for updating
> the `Works` table, but also the `Authors` and the `Works_Authors`
> tables. Also, note that some information that is kept in separate
> columns in our database, was stored in a single column in the
> exported file you are working on (look at the `Publication` column).

> ## Backing Up with SQL {.challenge}
>
> SQLite has several administrative commands that aren't part of the
> SQL standard.  One of them is `.dump`, which prints the SQL commands
> needed to re-create the database.  Another is `.load`, which reads a
> file created by `.dump` and restores the database.  A colleague of
> yours thinks that storing dump files (which are text) in version
> control is a good way to track and manage changes to the database.
> What are the pros and cons of this approach?  (Hint: records aren't
> stored in any particular order.)

[CREATE-TABLE]: https://www.sqlite.org/lang_createtable.html
[DROP-TABLE]: https://www.sqlite.org/lang_droptable.html
