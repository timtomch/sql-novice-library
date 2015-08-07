---
layout: page
title: Databases and SQL - Library edition
subtitle: Aggregation
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Define "aggregation" and give examples of its use.
> *   Write queries that compute aggregated values.
> *   Trace the execution of a query that performs aggregation.
> *   Explain how missing data is handled during aggregation.

To get an estimate of the amount of time it will take to digitize our collection, 
we now want to know how many books by a given publisher are in our collection, 
as well as calculate the total number of pages contained in the collection. 
We know how to select all of the page numbers from the `Works` table:

~~~ {.sql}
SELECT Pages FROM Works;
~~~

<table>
<TR><TH>Pages</TH>
</TR>
<TR><TD>578</TD>
</TR>
<TR><TD>-null-</TD>
</TR>
<TR><TD>532</TD>
</TR>
<TR><TD>503</TD>
</TR>
<TR><TD>258</TD>
</TR>
<TR><TD>685</TD>
</TR>
<TR><TD>534</TD>
</TR>
<TR><TD>400</TD>
</TR>
<TR><TD>460</TD>
</TR>
<TR><TD>309</TD>
</TR>
<TR><TD>857</TD>
</TR>
<TR><TD>320</TD>
</TR>
<TR><TD>440</TD>
</TR>
<TR><TD>218</TD>
</TR>
<TR><TD>501</TD>
</TR>
<TR><TD>-null-</TD>
</TR>
<TR><TD>708</TD>
</TR>
<TR><TD>760</TD>
</TR>
<TR><TD>691</TD>
</TR>
<TR><TD>321</TD>
</TR>
</table>

but to combine them,
we must use an [aggregation function](reference.html#aggregation-function)
such as `min` or `max`.
Each of these functions takes a set of records as input,
and produces a single record as output.
For example, the following statements display the smallest and the largest books in our collection:

~~~ {.sql}
SELECT Title, min(Pages) FROM Works;
~~~

<table>
<TR><TH>Title</TH>
<TH>min(Pages)</TH>
</TR>
<TR><TD>Beginning SQL queries</TD>
<TD>218</TD>
</TR>
</table>

<!--<img src="fig/sql-aggregation.svg" alt="SQL Aggregation" />-->

~~~ {.sql}
SELECT Title, max(Pages) FROM Works;
~~~

<table>
<TR><TH>Title</TH>
<TH>max(Pages)</TH>
</TR>
<TR><TD>SQL bible</TD>
<TD>857</TD>
</TR>
</table>

`min` and `max` are just two of
the aggregation functions built into SQL.
Three others are `avg`,
`count`,
and `sum`:

~~~ {.sql}
SELECT avg(Pages) FROM Works;
~~~

<table>
<TR><TH>avg(Pages)</TH>
</TR>
<TR><TD>504.166666666667</TD>
</TR>
</table>

~~~ {.sql}
SELECT count(Title) FROM Works Where Publisher='Wiley';
~~~

<table>
<TR><TH>count(Title)</TH>
</TR>
<TR><TD>7</TD>
</TR>
</table>

~~~ {.sql}
SELECT sum(Pages) FROM Works Where Publisher='Wiley';
~~~

<table>
<TR><TH>sum(Pages)</TH>
</TR>
<TR><TD>3666</TD>
</TR>
</table>

We used `count(Title)` here,
but we could just as easily have counted `ISBN`
or any other field in the table,
or even used `count(*)`,
since the function doesn't care about the values themselves,
just how many values there are.

SQL lets us do several aggregations at once.
We can, for example, find the range of book sizes published by O'Reilly:

~~~ {.sql}
SELECT min(Pages), max(Pages) FROM Works Where Publisher='O''Reilly';
~~~

<table>
<TR><TH>min(Pages)</TH>
<TH>max(Pages)</TH>
</TR>
<TR><TD>320</TD>
<TD>691</TD>
</TR>
</table>

We can also combine aggregated results with raw results,
although the output might surprise you:

~~~ {.sql}
SELECT Title, count(*) FROM Works Where Publisher='O''Reilly';
~~~

<table>
<TR><TH>Title</TH>
<TH>count(*)</TH>
</TR>
<TR><TD>MySQL in a nutshell</TD>
<TD>6</TD>
</TR>
</table>

Why does this title appear rather than any other published by O'Reilly?
The answer is that when it has to aggregate a field,
but isn't told how to,
the database manager chooses an actual value from the input set.
It might use the first one processed,
the last one,
or something else entirely.

Another important fact is that when there are no values to aggregate --- for example, where there are no rows satisfying the `WHERE` clause ---
aggregation's result is "don't know"
rather than zero or some other arbitrary value:

~~~ {.sql}
SELECT min(Pages), max(Pages) FROM Works Where Publisher='Hachette';
~~~

<table>
<TR><TH>min(Pages)</TH>
<TH>max(Pages)</TH>
</TR>
<TR><TD>-null-</TD>
<TD>-null-</TD>
</TR>
</table>

One final important feature of aggregation functions is that
they are inconsistent with the rest of SQL in a very useful way.
If we add two values,
and one of them is null,
the result is null.
By extension,
if we use `sum` to add all the values in a set,
and any of those values are null,
the result should also be null.
It's much more useful,
though,
for aggregation functions to ignore null values
and only combine those that are non-null.
This behavior lets us write our queries as:

~~~ {.sql}
SELECT Title, min(Pages) FROM Works;
~~~

<table>
<TR><TH>Title</TH>
<TH>min(Pages)</TH>
</TR>
<TR><TD>Beginning SQL queries</TD>
<TD>218</TD>
</TR>
</table>

instead of always having to filter explicitly:

~~~ {.sql}
SELECT Title, min(Pages) FROM Works WHERE Pages IS NOT NULL;
~~~

<table>
<TR><TH>Title</TH>
<TH>min(Pages)</TH>
</TR>
<TR><TD>Beginning SQL queries</TD>
<TD>218</TD>
</TR>
</table>

Aggregating all records at once doesn't always make sense.
For example, if we want to get a breakdown of how many books are in the collection for each publisher, we cannot write:

~~~ {.sql}
SELECT Publisher, count(Title) FROM Works;
~~~

<table>
<TR><TH>Publisher</TH>
<TH>count(Title)</TH>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>20</TD>
</TR>
</table>

because the database manager selects a single arbitrary publisher name 
rather than aggregating results separately for each publisher. 
Since in our small example there are only ten different publishers, 
we could write ten queries of the form:

~~~ {.sql}
SELECT Publisher, count(Title) FROM Works WHERE Publisher='Faber & Faber';
~~~

but this would be tedious, and it wouldn't be practical against a real-life database
containing many thousands different publishers.

What we need to do is tell the database manager to aggregate the number of titles for each publisher separately
using a `GROUP BY` clause:

~~~ {.sql}
SELECT Publisher, count(Title) FROM Works GROUP BY Publisher;
~~~

<table>
<TR><TH>Publisher</TH>
<TH>count(Title)</TH>
</TR>
<TR><TD>Apress</TD>
<TD>1</TD>
</TR>
<TR><TD>Belknap Press</TD>
<TD>1</TD>
</TR>
<TR><TD>Faber &amp; Faber</TD>
<TD>1</TD>
</TR>
<TR><TD>McGraw-Hill</TD>
<TD>1</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>6</TD>
</TR>
<TR><TD>Peachpit</TD>
<TD>1</TD>
</TR>
<TR><TD>Sams</TD>
<TD>1</TD>
</TR>
<TR><TD>South-Western</TD>
<TD>1</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>7</TD>
</TR>
</table>

`GROUP BY` does exactly what its name implies:
groups all the records with the same value for the specified field together
so that aggregation can process each batch separately.
Since all the records in each batch have the same value for `Publisher`,
it no longer matters that the database manager
is picking an arbitrary one to display
alongside the count of `Title` values.

Just as we can sort by multiple criteria at once,
we can also group by multiple criteria.
To get a yearly breakdown of the number of titles published by each publisher, 
we just add another field to the `GROUP BY` clause:

~~~ {.sql}
SELECT Publisher, Date, count(Title) FROM Works GROUP BY Publisher, Date;
~~~

<table>
<TR><TH>Publisher</TH>
<TH>Date</TH>
<TH>count(Title)</TH>
</TR>
<TR><TD>Apress</TD>
<TD>2008</TD>
<TD>1</TD>
</TR>
<TR><TD>Belknap Press</TD>
<TD>2014</TD>
<TD>1</TD>
</TR>
<TR><TD>Faber &amp; Faber</TD>
<TD>2014</TD>
<TD>1</TD>
</TR>
<TR><TD>McGraw-Hill</TD>
<TD>2009</TD>
<TD>1</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2004</TD>
<TD>1</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2005</TD>
<TD>1</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2009</TD>
<TD>2</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2010</TD>
<TD>1</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2013</TD>
<TD>1</TD>
</TR>
<TR><TD>Peachpit</TD>
<TD>2005</TD>
<TD>1</TD>
</TR>
<TR><TD>Sams</TD>
<TD>2013</TD>
<TD>1</TD>
</TR>
<TR><TD>South-Western</TD>
<TD>2008</TD>
<TD>1</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2005</TD>
<TD>1</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2008</TD>
<TD>1</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2010</TD>
<TD>1</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2011</TD>
<TD>2</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2013</TD>
<TD>2</TD>
</TR>
</table>

Note that we have added `Date` to the list of fields displayed,
since the results wouldn't make much sense otherwise.

Let's wrap up our collection analysis query:

~~~ {.sql}
SELECT	 Publisher, Date, count(Title), sum(Pages) FROM Works 
WHERE 	 Pages IS NOT NULL 
GROUP BY Publisher, Date 
ORDER BY Publisher ASC, Date DESC;
~~~

<table>
<TR><TH>Publisher</TH>
<TH>Date</TH>
<TH>count(Title)</TH>
<TH>sum(Pages)</TH>
</TR>
<TR><TD>Apress</TD>
<TD>2008</TD>
<TD>1</TD>
<TD>218</TD>
</TR>
<TR><TD>Belknap Press</TD>
<TD>2014</TD>
<TD>1</TD>
<TD>685</TD>
</TR>
<TR><TD>Faber &amp; Faber</TD>
<TD>2014</TD>
<TD>1</TD>
<TD>258</TD>
</TR>
<TR><TD>McGraw-Hill</TD>
<TD>2009</TD>
<TD>1</TD>
<TD>534</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2013</TD>
<TD>1</TD>
<TD>532</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2010</TD>
<TD>1</TD>
<TD>503</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2009</TD>
<TD>2</TD>
<TD>898</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2005</TD>
<TD>1</TD>
<TD>321</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
<TD>2004</TD>
<TD>1</TD>
<TD>691</TD>
</TR>
<TR><TD>Peachpit</TD>
<TD>2005</TD>
<TD>1</TD>
<TD>460</TD>
</TR>
<TR><TD>South-Western</TD>
<TD>2008</TD>
<TD>1</TD>
<TD>309</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2013</TD>
<TD>1</TD>
<TD>760</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2011</TD>
<TD>2</TD>
<TD>1108</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2010</TD>
<TD>1</TD>
<TD>440</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2008</TD>
<TD>1</TD>
<TD>857</TD>
</TR>
<TR><TD>Wiley</TD>
<TD>2005</TD>
<TD>1</TD>
<TD>501</TD>
</TR>
</table>

Looking more closely,
this query:

1.  selected records from the `Works` table where the `Pages` field was not null;

2.  grouped those records into subsets so that the `Publisher`and `Date` values in each subset were the same;

3.  ordered those subsets first by `Publisher`, and then within each sub-group by `Date; and

4.  counted the number of records in each subset, 
	calculated the sum of `Pages` in each, 
	and chose a `Publisher` and `Date` value from each to display
	(it doesn't matter which ones, since they're all equal in each subset)

> ## Counting Books Published in 2011 {.challenge}
>
> How many books were published in 2011, and what was their average length?

> ## Averaging with NULL {.challenge}
>
> The average of a set of values is the sum of the values divided by the number of values. 
> Does this mean that the `avg`function returns 2.0 or 3.0 when given the values 1.0, `NULL`, and 5.0?

> ## Ordering When Concatenating {.challenge}
>
> The function `group_concat(field, separator)`
> concatenates all the values in a field
> using the specified separator character
> (or ',' if the separator isn't specified).
> Use this to produce a one-line list of publishers' names, such as:
>
> ~~~ {.sql}
> O'Reilly, Wiley, Faber &amp; Faber, Belknap Press, ...
> ~~~
>
> Can you find a way to order the list alphabetically?
