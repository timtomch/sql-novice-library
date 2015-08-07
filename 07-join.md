---
layout: page
title: Databases and SQL
subtitle: Combining Data
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Explain the operation of a query that joins two tables.
> *   Explain how to restrict the output of a query containing a join to only include meaningful combinations of values.
> *   Write queries that join tables on equal keys.
> *   Explain what primary and foreign keys are, and why they are useful.

So far we haven't been able to display the authors' names in our query results, because those names are not in the `Works` but in the `Authors` table. What's worse, because there can be many authors to a title and many title associated with each author, there is a third table, `Works_Authors`that's taking care of this many-to-many relationship. How can these tables be joined?

The SQL command to do this is `JOIN`.
To see how it works, let's start with the somewhat easier case of the `Items` table and try joining it to the `Works` table:


~~~ {.sql}
SELECT * FROM Items JOIN Works;
~~~

<table>
<TR><TH>Item_ID</TH>
<TH>Work_ID</TH>
<TH>Barcode</TH>
<TH>Acquired</TH>
<TH>Status</TH>
<TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>1</TD>
<TD>081722942611</TD>
<TD>2009</TD>
<TD>Loaned</TD>
<TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>3rd ed.</TD>
<TD>578</TD>
</TR>
<TR><TD>1</TD>
<TD>1</TD>
<TD>081722942611</TD>
<TD>2009</TD>
<TD>Loaned</TD>
<TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>1</TD>
<TD>1</TD>
<TD>081722942611</TD>
<TD>2009</TD>
<TD>Loaned</TD>
<TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>1</TD>
<TD>1</TD>
<TD>081722942611</TD>
<TD>2009</TD>
<TD>Loaned</TD>
<TD>4</TD>
<TD>Using SQLite</TD>
<TD>9780596521189</TD>
<TD>2010</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>503</TD>
</TR>
<TR><TD>1</TD>
<TD>1</TD>
<TD>081722942611</TD>
<TD>2009</TD>
<TD>Loaned</TD>
<TD>5</TD>
<TD>Geek sublime</TD>
<TD>9780571310302</TD>
<TD>2014</TD>
<TD>London</TD>
<TD>Faber &amp; Faber</TD>
<TD></TD>
<TD>258</TD>
</TR>
</table>

The result above was truncated for display because... the query returned a list of 800 records! In fact,
`JOIN` creates
the [cross product](reference.html#cross-product)
of two tables,
i.e.,
it joins each record of one table with each record of the other table
to give all possible combinations.
Since there are 20 records in `Works` and 40 in `Items`, the join's output has 20*40=800 records. 
And since `Works` has 8 fields and `Items` has 5, the output has 8+5=13 fields.

What the join *hasn't* done is
figure out if the records being joined have anything to do with each other.
It has no way of knowing whether they do or not until we tell it how.
To do that, we add a clause specifying that we're only interested in 
combinations where `Work_ID matches in both tables:

~~~ {.sql}
SELECT * FROM Items JOIN Works ON Items.Work_ID=Works.Work_ID LIMIT 10;
~~~

<table>
<TR><TH>Item_ID</TH>
<TH>Work_ID</TH>
<TH>Barcode</TH>
<TH>Acquired</TH>
<TH>Status</TH>
<TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>1</TD>
<TD>081722942611</TD>
<TD>2009</TD>
<TD>Loaned</TD>
<TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>3rd ed.</TD>
<TD>578</TD>
</TR>
<TR><TD>2</TD>
<TD>1</TD>
<TD>492437609065</TD>
<TD>2011</TD>
<TD>On shelf</TD>
<TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>3rd ed.</TD>
<TD>578</TD>
</TR>
<TR><TD>3</TD>
<TD>2</TD>
<TD>172480710952</TD>
<TD>2013</TD>
<TD>On shelf</TD>
<TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>4</TD>
<TD>3</TD>
<TD>708014968732</TD>
<TD>2013</TD>
<TD>Missing</TD>
<TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>5</TD>
<TD>3</TD>
<TD>819783404942</TD>
<TD>2014</TD>
<TD>Loaned</TD>
<TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>6</TD>
<TD>4</TD>
<TD>257370237291</TD>
<TD>2010</TD>
<TD>Missing</TD>
<TD>4</TD>
<TD>Using SQLite</TD>
<TD>9780596521189</TD>
<TD>2010</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>503</TD>
</TR>
<TR><TD>7</TD>
<TD>5</TD>
<TD>002905925356</TD>
<TD>2014</TD>
<TD>Loaned</TD>
<TD>5</TD>
<TD>Geek sublime</TD>
<TD>9780571310302</TD>
<TD>2014</TD>
<TD>London</TD>
<TD>Faber &amp; Faber</TD>
<TD></TD>
<TD>258</TD>
</TR>
<TR><TD>8</TD>
<TD>5</TD>
<TD>964583604781</TD>
<TD>2014</TD>
<TD>Loaned</TD>
<TD>5</TD>
<TD>Geek sublime</TD>
<TD>9780571310302</TD>
<TD>2014</TD>
<TD>London</TD>
<TD>Faber &amp; Faber</TD>
<TD></TD>
<TD>258</TD>
</TR>
<TR><TD>9</TD>
<TD>6</TD>
<TD>701630524534</TD>
<TD>2014</TD>
<TD>Loaned</TD>
<TD>6</TD>
<TD>Capital in the 21st century</TD>
<TD>9780674430006</TD>
<TD>2014</TD>
<TD>Cambridge</TD>
<TD>Belknap Press</TD>
<TD></TD>
<TD>685</TD>
</TR>
<TR><TD>10</TD>
<TD>6</TD>
<TD>722040919616</TD>
<TD>2014</TD>
<TD>On shelf</TD>
<TD>6</TD>
<TD>Capital in the 21st century</TD>
<TD>9780674430006</TD>
<TD>2014</TD>
<TD>Cambridge</TD>
<TD>Belknap Press</TD>
<TD></TD>
<TD>685</TD>
</TR>
</table>

`ON` is very similar to `WHERE`,
and for all the queries in this lesson you can use them interchangeably.
There are differences in how they affect [outer joins][OUTER],
but that's beyond the scope of this lesson.
Once we add this to our query,
the database manager throws away records
that combined information about two different sites,
leaving us with just the ones we want.

Notice that we used `Table.field` to specify field names
in the output of the join.
We do this because tables can have fields with the same name,
and we need to be specific which ones we're talking about.

We can now use the same dotted notation
to select the three columns we actually want
out of our join:

~~~ {.sql}
SELECT Items.Barcode, Works.Title, Works.ISBN FROM Items JOIN Works ON Items.Work_ID=Works.Work_ID LIMIT 10;
~~~

<table>
<TR><TH>Barcode</TH>
<TH>Title</TH>
<TH>ISBN</TH>
</TR>
<TR><TD>081722942611</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
</TR>
<TR><TD>492437609065</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
</TR>
<TR><TD>172480710952</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
</TR>
<TR><TD>708014968732</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
</TR>
<TR><TD>819783404942</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
</TR>
<TR><TD>257370237291</TD>
<TD>Using SQLite</TD>
<TD>9780596521189</TD>
</TR>
<TR><TD>002905925356</TD>
<TD>Geek sublime</TD>
<TD>9780571310302</TD>
</TR>
<TR><TD>964583604781</TD>
<TD>Geek sublime</TD>
<TD>9780571310302</TD>
</TR>
<TR><TD>701630524534</TD>
<TD>Capital in the 21st century</TD>
<TD>9780674430006</TD>
</TR>
<TR><TD>722040919616</TD>
<TD>Capital in the 21st century</TD>
<TD>9780674430006</TD>
</TR>
</table>

We can now try to tackle the case of the `Authors` table. 
To list the contributors associated with the first item on the `Works` table 
(`Work_ID=1`, SQL in a nutshell 3rd ed.), we write:


~~~ {.sql}
SELECT Works_Authors.Role, Authors.Personal, Authors.Family 
FROM   Works_Authors 
JOIN   Authors 
ON     Authors.Author_ID=Works_Authors.Author_ID 
WHERE  Works_Authors.Work_ID=1;
~~~

<table>
<TR><TH>Role</TH>
<TH>Personal</TH>
<TH>Family</TH>
</TR>
<TR><TD>Author</TD>
<TD>Kevin E.</TD>
<TD>Kline</TD>
</TR>
<TR><TD>Contributor</TD>
<TD>Daniel</TD>
<TD>Kline</TD>
</TR>
<TR><TD>Contributor</TD>
<TD>Brand</TD>
<TD>Hunt</TD>
</TR>
</table>

Or inversely, if we want to list all the works that Allen G. Taylor (<code>Author_ID=4</code>) has authored or contributed to, we can write:


~~~ {.sql}
SELECT Works.Title, Works.Date, Works.Edition, Works_Authors.Role 
FROM   Works 
JOIN   Works_Authors 
ON     Works.Work_ID=Works_Authors.Work_ID 
WHERE  Works_Authors.Author_ID=4;
~~~

<table>
<TR><TH>Title</TH>
<TH>Date</TH>
<TH>Edition</TH>
<TH>Role</TH>
</TR>
<TR><TD>SQL for dummies</TD>
<TD>2013</TD>
<TD>8th ed.</TD>
<TD>Author</TD>
</TR>
<TR><TD>SQL for dummies</TD>
<TD>2010</TD>
<TD>7th ed.</TD>
<TD>Author</TD>
</TR>
<TR><TD>SQL all-in-one</TD>
<TD>2011</TD>
<TD>2nd ed.</TD>
<TD>Author</TD>
</TR>
<TR><TD>Access 2013 all-in-one</TD>
<TD>2013</TD>
<TD></TD>
<TD>Contributor</TD>
</TR>
</table>

We can tell which records from `Works`, <code>Authors</code>, <code>Items</code> and <code>Works_Authors</code> correspond with each other because those tables contain 
[primary keys](reference.html#primary-key)
and [foreign keys](reference.html#foreign-key).
A primary key is a value,
or combination of values,
that uniquely identifies each record in a table.
A foreign key is a value (or combination of values) from one table
that identifies a unique record in another table.
Another way of saying this is that
a foreign key is the primary key of one table
that appears in some other table.
In our database, `Works.Work_ID` is the primary key in the `Works` table, 
while `Items.Work_ID` is a foreign key relating the `Items` table's entries 
to entries in `Works`. 
The `Authors_Works` table contains only foreign keys relating to entries 
in the `Works` and `Authors` tables.

Most database designers believe that
every table should have a well-defined primary key.
They also believe that this key should be separate from the data itself,
so that if we ever need to change the data,
we only need to make one change in one place.
One easy way to do this is
to create an arbitrary, unique ID for each record
as we add it to the database.
This is actually very common:
those IDs have names like "student numbers" and "patient numbers",
and they almost always turn out to have originally been
a unique record identifier in some database system or other.
As the query below demonstrates,
SQLite [automatically numbers records][rowid] as they're added to tables,
and we can use those record numbers in queries:

~~~ {.sql}
SELECT rowid, * FROM Person;
~~~

<table>
</table>

> ## Listing Authors whose Name starts with K {.challenge}
>
> Write a query that lists all works written by people whose Family name start with the letter "K".

> ## Listing Popular Authors {.challenge}
>
> Write a query that lists all authors that have written at least one book that is currently on loan from the library.

> ## Who are the Authors of this Title? {.challenge}
>
> To which item does the barcode `722040919616` refer to, what is the title of this book and who are its authors?

[OUTER]: http://en.wikipedia.org/wiki/Join_%28SQL%29#Outer_join
[rowid]: https://www.sqlite.org/lang_createtable.html#rowid
