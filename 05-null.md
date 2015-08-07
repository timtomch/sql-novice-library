---
layout: page
title: Databases and SQL - Library edition
subtitle: Missing Data
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Explain how databases represent missing information.
> *   Explain the three-valued logic databases use when manipulating missing information.
> *   Write queries that handle missing information correctly.

Real-world data is never complete --- there are always holes.
Databases represent these holes using a special value called `null`.
`null` is not zero, `False`, or the empty string;
it is a one-of-a-kind value that means "nothing here".
Dealing with `null` requires a few special tricks
and some careful thinking.

To start,
let's have a look at the `Works` table.
There are a total of 20 records. The page number is missing for records #2 and #26 --— for them, the `Pages` field is null:


~~~ {.sql}
SELECT Work_ID, Title, Pages FROM Works;
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>578</TD>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>-null-</TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>532</TD>
</TR>
<TR><TD>4</TD>
<TD>Using SQLite</TD>
<TD>503</TD>
</TR>
<TR><TD>5</TD>
<TD>Geek sublime</TD>
<TD>258</TD>
</TR>
<TR><TD>6</TD>
<TD>Capital in the 21st century</TD>
<TD>685</TD>
</TR>
<TR><TD>7</TD>
<TD>SQL</TD>
<TD>534</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>400</TD>
</TR>
<TR><TD>9</TD>
<TD>SQL</TD>
<TD>460</TD>
</TR>
<TR><TD>10</TD>
<TD>A guide to SQL</TD>
<TD>309</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>857</TD>
</TR>
<TR><TD>12</TD>
<TD>Learning SQL</TD>
<TD>320</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>440</TD>
</TR>
<TR><TD>14</TD>
<TD>Beginning SQL queries</TD>
<TD>218</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>501</TD>
</TR>
<TR><TD>16</TD>
<TD>Microsoft SQL server 2012</TD>
<TD>-null-</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>760</TD>
</TR>
<TR><TD>19</TD>
<TD>SQL in a nutshell</TD>
<TD>691</TD>
</TR>
<TR><TD>20</TD>
<TD>MySQL in a nutshell</TD>
<TD>321</TD>
</TR>
</table>

Null doesn't behave like other values.
If we select the works that have less than 300 pages:

~~~ {.sql}
SELECT Work_ID, Title, Pages FROM Works WHERE Pages<300;
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>Pages</TH>
</TR>
<TR><TD>5</TD>
<TD>Geek sublime</TD>
<TD>258</TD>
</TR>
<TR><TD>14</TD>
<TD>Beginning SQL queries</TD>
<TD>218</TD>
</TR>
</table>

we get two results,
and if we select the ones that have 300 or more pages:

~~~ {.sql}
SELECT Work_ID, Title, Pages FROM Works WHERE Pages>=300;
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>578</TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>532</TD>
</TR>
<TR><TD>4</TD>
<TD>Using SQLite</TD>
<TD>503</TD>
</TR>
<TR><TD>6</TD>
<TD>Capital in the 21st century</TD>
<TD>685</TD>
</TR>
<TR><TD>7</TD>
<TD>SQL</TD>
<TD>534</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>400</TD>
</TR>
<TR><TD>9</TD>
<TD>SQL</TD>
<TD>460</TD>
</TR>
<TR><TD>10</TD>
<TD>A guide to SQL</TD>
<TD>309</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>857</TD>
</TR>
<TR><TD>12</TD>
<TD>Learning SQL</TD>
<TD>320</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>440</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>501</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>760</TD>
</TR>
<TR><TD>19</TD>
<TD>SQL in a nutshell</TD>
<TD>691</TD>
</TR>
<TR><TD>20</TD>
<TD>MySQL in a nutshell</TD>
<TD>321</TD>
</TR>
</table>

we get sixteen results. Records #2 and #16 aren't in either set of results. 
The reason is that `NULL<300` 
is neither true nor false: null means, "We don't know," 
and if we don't know the value on the left side of a comparison, 
we don't know whether the comparison is true or false. 
Since databases represent "don't know" as null, the value of `NULL<300` is actually NULL. 

`NULL>=300` is also null because we can't answer to that question either. And since the only records kept by a 
`WHERE` are those for which the test is true, records #2 and #16 aren't included in either set of results.

Comparisons aren't the only operations that behave this way with nulls.
`1+NULL` is `NULL`,
`5*NULL` is `NULL`,
and so on.
In particular,
comparing things to null with = and != produces null:

~~~ {.sql}
SELECT Work_ID, Title, Pages FROM Works WHERE Pages=NULL;
~~~

produces no output, and neither does:

~~~ {.sql}
SELECT Work_ID, Title, Pages FROM Works WHERE Pages!=NULL;
~~~

To check whether a value is `null` or not,
we must use a special test `IS NULL`:

~~~ {.sql}
SELECT Work_ID, Title, Pages FROM Works WHERE Pages IS NULL;
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>Pages</TH>
</TR>
<TR><TD>2</TD>
<TD>SQL for dummies</TD>
<TD>-null-</TD>
</TR>
<TR><TD>16</TD>
<TD>Microsoft SQL server 2012</TD>
<TD>-null-</TD>
</TR>
</table>

or its inverse `IS NOT NULL`:

~~~ {.sql}
SELECT Work_ID, Title, Pages FROM Works WHERE Pages IS NOT NULL;
~~~

<table>
<TR><TH>Work_ID</TH>
<TH>Title</TH>
<TH>Pages</TH>
</TR>
<TR><TD>1</TD>
<TD>SQL in a nutshell</TD>
<TD>578</TD>
</TR>
<TR><TD>3</TD>
<TD>PHP &amp; MySQL</TD>
<TD>532</TD>
</TR>
<TR><TD>4</TD>
<TD>Using SQLite</TD>
<TD>503</TD>
</TR>
<TR><TD>5</TD>
<TD>Geek sublime</TD>
<TD>258</TD>
</TR>
<TR><TD>6</TD>
<TD>Capital in the 21st century</TD>
<TD>685</TD>
</TR>
<TR><TD>7</TD>
<TD>SQL</TD>
<TD>534</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>400</TD>
</TR>
<TR><TD>9</TD>
<TD>SQL</TD>
<TD>460</TD>
</TR>
<TR><TD>10</TD>
<TD>A guide to SQL</TD>
<TD>309</TD>
</TR>
<TR><TD>11</TD>
<TD>SQL bible</TD>
<TD>857</TD>
</TR>
<TR><TD>12</TD>
<TD>Learning SQL</TD>
<TD>320</TD>
</TR>
<TR><TD>13</TD>
<TD>SQL for dummies</TD>
<TD>440</TD>
</TR>
<TR><TD>14</TD>
<TD>Beginning SQL queries</TD>
<TD>218</TD>
</TR>
<TR><TD>15</TD>
<TD>Beginning SQL</TD>
<TD>501</TD>
</TR>
<TR><TD>17</TD>
<TD>SQL all-in-one</TD>
<TD>708</TD>
</TR>
<TR><TD>18</TD>
<TD>Access 2013 all-in-one</TD>
<TD>760</TD>
</TR>
<TR><TD>19</TD>
<TD>SQL in a nutshell</TD>
<TD>691</TD>
</TR>
<TR><TD>20</TD>
<TD>MySQL in a nutshell</TD>
<TD>321</TD>
</TR>
</table>

Null values can cause headaches wherever they appear.
For example, suppose we want to find all books about SQL that have less than 500 pages. 
It's natural to write the query like this:

~~~ {.sql}
SELECT * FROM Works WHERE Title LIKE "%SQL%" AND Pages<500;
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
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD></TD>
<TD>400</TD>
</TR>
<TR><TD>9</TD>
<TD>SQL</TD>
<TD>0321334175</TD>
<TD>2005</TD>
<TD>Berkeley</TD>
<TD>Peachpit</TD>
<TD>2nd ed.</TD>
<TD>460</TD>
</TR>
<TR><TD>10</TD>
<TD>A guide to SQL</TD>
<TD>9780324597684</TD>
<TD>2008</TD>
<TD>Mason</TD>
<TD>South-Western</TD>
<TD>8th ed.</TD>
<TD>309</TD>
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
<TR><TD>14</TD>
<TD>Beginning SQL queries</TD>
<TD>9781590599433</TD>
<TD>2008</TD>
<TD>Berkeley</TD>
<TD>Apress</TD>
<TD></TD>
<TD>218</TD>
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


but this query filters omits the records where we don't know the number of pages.
Once again, the reason is that when `Pages` is `NULL`, the `<` comparison produces `NULL`, 
so the record isn't kept in our results. If we want to keep these records we need to add an explicit check:


~~~ {.sql}
SELECT * FROM Works WHERE Title LIKE "%SQL%" AND (Pages<500 OR Pages IS NULL);
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
<TD>-null-</TD>
</TR>
<TR><TD>8</TD>
<TD>Discovering SQL</TD>
<TD>9781118002674</TD>
<TD>2011</TD>
<TD>Indianapolis</TD>
<TD>Wiley</TD>
<TD>-null-</TD>
<TD>400</TD>
</TR>
<TR><TD>9</TD>
<TD>SQL</TD>
<TD>0321334175</TD>
<TD>2005</TD>
<TD>Berkeley</TD>
<TD>Peachpit</TD>
<TD>2nd ed.</TD>
<TD>460</TD>
</TR>
<TR><TD>10</TD>
<TD>A guide to SQL</TD>
<TD>9780324597684</TD>
<TD>2008</TD>
<TD>Mason</TD>
<TD>South-Western</TD>
<TD>8th ed.</TD>
<TD>309</TD>
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
<TR><TD>14</TD>
<TD>Beginning SQL queries</TD>
<TD>9781590599433</TD>
<TD>2008</TD>
<TD>Berkeley</TD>
<TD>Apress</TD>
<TD>-null-</TD>
<TD>218</TD>
</TR>
<TR><TD>16</TD>
<TD>Microsoft SQL server 2012</TD>
<TD>9780132977661</TD>
<TD>2013</TD>
<TD>Indianapolis</TD>
<TD>Sams</TD>
<TD>-null-</TD>
<TD>-null-</TD>
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

We still have to decide whether this is the right thing to do or not.
If we want to be absolutely sure that we aren't including any books with less than 500 pages, 
we need to exclude all the records for which we don't know the number of pages.

In contrast to arithmetic or Boolean operators, aggregation functions that combine multiple values, such as `min`, `max` or `avg`, *ignore* `null` values. In the majority of cases, this is a desirable output: for example, unknown values are thus not affecting our data when we are averaging it. Aggregation functions will be addressed in more detail in [the next section](06-agg.html).

> ## Sorting by Known Number of Pages {.challenge}
>
> Write a query that sorts the records in `Works` by the number of pages, 
> omitting entries for which this information is not known (i.e., is NULL).

> ## NULL in a Set {.challenge}
>
> What do you expect the query:
>
> ~~~ {.sql}
> SELECT * FROM Works WHERE Edition in ("1st ed.", NULL);
> ~~~
>
> to produce?
> What does it actually produce?

> ## Pros and Cons of Sentinels {.challenge}
>
> Some database designers prefer to use
> a [sentinel value](reference.html#sentinel-value))
> to mark missing data rather than `null`.
> For example,
> they will use the date "0000-00-00" to mark a missing date,
> or -1 to mark a missing page number
> What does this simplify?
> What burdens or risks does it introduce?
