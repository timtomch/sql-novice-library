---
layout: page
title: Databases and SQL - Library edition
subtitle: Filtering
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Write queries that select records that satisfy user-specified conditions.
> *   Explain the order in which the clauses in a query are executed.

One of the most powerful features of a database is
the ability to [filter](reference.html#filter) data,
i.e.,
to select only those records that match certain criteria.
For example, suppose we want to list all the books in the catalogue that have been published by Wiley. 
We can select these records from the `Works` table by using a `WHERE` clause in our query:

~~~ {.sql}
SELECT * FROM Works WHERE Publisher="Wiley";
~~~
<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>9780470229064</TD>
<TD>2008</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>857</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>9780470557419</TD>
<TD>2010</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>7th ed.</TD>
<TD>440</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>0764577328</TD>
<TD>2005</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>501</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>9780470929964</TD>
<TD>2011</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>9781118510551</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>760</TD>
</TR>
</table>

The database manager executes this query in two stages.
First,
it checks at each row in the `Works` table
to see which ones satisfy the `WHERE`.
It then uses the column names following the `SELECT` keyword
to determine what columns to display.

This processing order means that
we can filter records using `WHERE`
based on values in columns that aren't then displayed:

~~~ {.sql}
SELECT Title FROM Works WHERE Publisher="Wiley";
~~~

<table>
<TR><TH>Title</TH>
</TR>
<TR><TD>SQL for dummies</TD>
</TR>
<TR><TD>Discovering SQL</TD>
</TR>
<TR><TD>SQL bible</TD>
</TR>
<TR><TD>SQL for dummies</TD>
</TR>
<TR><TD>Beginning SQL</TD>
</TR>
<TR><TD>SQL all-in-one</TD>
</TR>
<TR><TD>Access 2013 all-in-one</TD>
</TR>
</table>

We can use many other Boolean operators to filter our data. For example, we can display only titles that have been published by Wiley in 2011 or later:

~~~ {.sql}
SELECT * FROM Works WHERE (Publisher='Wiley') AND (Date>='2011');
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>9780470929964</TD>
<TD>2011</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>9781118510551</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>760</TD>
</TR>
</table>


(The parentheses around the individual tests aren't strictly required, but they help make the query easier to read.)

> ## Date types {.callout}
>
> Most database managers have a special data type for dates.
> In fact, many have two:
> one for dates,
> such as "May 31, 1971",
> and one for durations,
> such as "31 days".
> SQLite doesn't:
> instead,
> it stores dates as either text
> (in the ISO-8601 standard format "YYYY-MM-DD HH:MM:SS.SSSS"),
> real numbers
> (the number of days since November 24, 4714 BCE),
> or integers
> (the number of seconds since midnight, January 1, 1970).
> If this sounds complicated,
> it is,
> but not nearly as complicated as figuring out
> [historical dates in Sweden](http://en.wikipedia.org/wiki/Swedish_calendar).

If we want to list all books published either by Wiley or O'Reilly, we can combine the query using `OR`. Note in passing how the apostrophe in the name O'Reilly is escaped:

~~~ {.sql}
SELECT * FROM Works WHERE Publisher='Wiley' OR Publisher='O''Reilly';
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>3rd ed.</TD>
<TD>578</TD>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>4</TD>
<TD>Using SQLite</TD>
<TD>9780596521189</TD>
<TD>2010</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>503</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>9780470229064</TD>
<TD>2008</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>857</TD>
</TR>
<TR><TD>12</TD>
<TD>Learning SQL</TD>
<TD>9780596520830</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>320</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>9780470557419</TD>
<TD>2010</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>7th ed.</TD>
<TD>440</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>0764577328</TD>
<TD>2005</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>501</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>9780470929964</TD>
<TD>2011</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>9781118510551</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>760</TD>
</TR>
<TR><TD>19</TD>
<TD>SQL in a nutshell</TD>
<TD>0596004818</TD>
<TD>2004</TD>
<TD>Cambridge</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>691</TD>
</TR>
<TR><TD>20</TD>
<TD>MySQL in a nutshell</TD>
<TD>0596007892</TD>
<TD>2005</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>321</TD>
</TR>
</table>

Alternatively,
we can use `IN` to see if a value is in a specific set:

~~~ {.sql}
SELECT * FROM Works WHERE Publisher IN('Wiley','O''Reilly');
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>3rd ed.</TD>
<TD>578</TD>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>4</TD>
<TD>Using SQLite</TD>
<TD>9780596521189</TD>
<TD>2010</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>503</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>9780470229064</TD>
<TD>2008</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>857</TD>
</TR>
<TR><TD>12</TD>
<TD>Learning SQL</TD>
<TD>9780596520830</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>320</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>9780470557419</TD>
<TD>2010</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>7th ed.</TD>
<TD>440</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>0764577328</TD>
<TD>2005</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>501</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>9780470929964</TD>
<TD>2011</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>9781118510551</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>760</TD>
</TR>
<TR><TD>19</TD>
<TD>SQL in a nutshell</TD>
<TD>0596004818</TD>
<TD>2004</TD>
<TD>Cambridge</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>691</TD>
</TR>
<TR><TD>20</TD>
<TD>MySQL in a nutshell</TD>
<TD>0596007892</TD>
<TD>2005</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>321</TD>
</TR>

</table>

We can combine `AND` with `OR`,
but we need to be careful about which operator is executed first.
If we *don't* use parentheses,
we get this:

~~~ {.sql}
SELECT * FROM Works WHERE Publisher='Wiley' OR Publisher='O''Reilly' AND Date>=2011;
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>9780470229064</TD>
<TD>2008</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>857</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>9780470557419</TD>
<TD>2010</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>7th ed.</TD>
<TD>440</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>0764577328</TD>
<TD>2005</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>501</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>9780470929964</TD>
<TD>2011</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>9781118510551</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>760</TD>
</TR>
</table>

which is a list of the books published either by Wiley at any given time and those by O'Reilly in 2011 or later. We probably want this instead:

~~~ {.sql}
SELECT * FROM Works WHERE (Publisher='Wiley' OR Publisher='O''Reilly') AND Date>=2011;
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>9780470929964</TD>
<TD>2011</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>9781118510551</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>760</TD>
</TR>
</table>

We can also filter by partial matches.
For example,
if we want to know something just about the site names beginning with "DR" we can use the `LIKE` keyword.
The percent symbol acts as a [wildcard](reference.html#wildcard),
matching any characters in that place.
It can be used at the beginning, middle, or end of the string:

~~~ {.sql}
SELECT * FROM Works WHERE Publisher LIKE "%y";
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>ISBN</TH>
<TH>Date</TH>
<TH>Place</TH>
<TH>Publisher</TH>
<TH>Edition</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>9780596518844</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>3rd ed.</TD>
<TD>578</TD>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>9781118607961</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>8th ed.</TD>
<TD></TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>9781449325572</TD>
<TD>2013</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>532</TD>
</TR>
<TR><TD>4</TD>
<TD>Using SQLite</TD>
<TD>9780596521189</TD>
<TD>2010</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>503</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>9780470229064</TD>
<TD>2008</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>857</TD>
</TR>
<TR><TD>12</TD>
<TD>Learning SQL</TD>
<TD>9780596520830</TD>
<TD>2009</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>320</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>9780470557419</TD>
<TD>2010</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>7th ed.</TD>
<TD>440</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>0764577328</TD>
<TD>2005</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>501</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>9780470929964</TD>
<TD>2011</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD>2nd ed.</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>9781118510551</TD>
<TD>2013</TD>
<TD>Hoboken</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>760</TD>
</TR>
<TR><TD>19</TD>
<TD>SQL in a nutshell</TD>
<TD>0596004818</TD>
<TD>2004</TD>
<TD>Cambridge</TD>
<TD>O&#39;Reilly</TD>
<TD>2nd ed.</TD>
<TD>691</TD>
</TR>
<TR><TD>20</TD>
<TD>MySQL in a nutshell</TD>
<TD>0596007892</TD>
<TD>2005</TD>
<TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
<TD>1st ed.</TD>
<TD>321</TD>
</TR>
</table>


Finally,
we can use `DISTINCT` with `WHERE`
to give a second level of filtering:

~~~ {.sql}
SELECT DISTINCT Date, Publisher FROM Works WHERE (Publisher='Wiley' OR Publisher='O''Reilly') AND Date>=2011;
~~~

<table>
<TR><TH>Date</TH>
<TH>Publisher</TH>
</TR>
<TR><TD>2013</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>2013</TD>
<TD>O&#39;Reilly</TD>
</TR>
<TR><TD>2011</TD>
<TD>Wiley</TD>
</TR>
</table>

But remember:
`DISTINCT` is applied to the values displayed in the chosen columns,
not to the entire rows as they are being processed.

> What we have just done is how most people "grow" their SQL queries.
> We started with something simple that did part of what we wanted,
> then added more clauses one by one,
> testing their effects as we went.
> This is a good strategy --- in fact,
> for complex queries it's often the *only* strategy --- but
> it depends on quick turnaround,
> and on us recognizing the right answer when we get it.
>
> The best way to achieve quick turnaround is often
> to put a subset of data in a temporary database
> and run our queries against that,
> or to fill a small database with synthesized records.
> For example,
> instead of trying our queries against an actual database of 20 million bibliographic records,
> we could run it against a sample of ten thousand,
> or write a small program to generate ten thousand random (but plausible) records
> and use that.

> ## Weeding the Library by Date {.challenge}
>
> The library wants to weed its collection and you need to review all books published after 2004 (earlier books have already been reviewed) 
> but before 2010. Write a query that selects the titles that need to be reviewed.


> ## Finding Recent Titles on SQL {.challenge}
>
> Write a query that lists all books in the database that have the word "SQL" in their title and that have been published after 2010.
> Hint: we are not looking for titles that are exactly "SQL", but those that contain "SQL" somewhere in their title. 
> You might want to use `LIKE` to do that.


> ## Matching Patterns {.challenge}
>
> Which of these expressions are true?
>
> * `'a' LIKE 'a'`
> * `'a' LIKE '%a'`
> * `'beta' LIKE '%a'`
> * `'alpha' LIKE 'a%%'`
> * `'alpha' LIKE 'a%p%'`
