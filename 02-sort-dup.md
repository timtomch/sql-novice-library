---
layout: page
title: Databases and SQL
subtitle: Sorting and Removing Duplicates
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Write queries that display results in a particular order.
> *   Write queries that eliminate duplicate values from data.

Data is often redundant, so queries often return redundant information. For example, say we were interested in listing all the publishers that are represented in our catalogue. If we select the `Publisher` from the `Works` table, we get this:

~~~ {.sql}
SELECT Publisher FROM Works;
~~~
<table>
<TR><TH>Publisher</TH>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Faber &amp; Faber</TD>
</TR>
<TR><TD>Belknap Press</TD>
</TR>
<TR><TD>McGraw-Hill</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>Peachpit</TD>
</TR>
<TR><TD>South-Western</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>Apress</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>Sams</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
</table>

We can eliminate the redundant output to make the result more readable by adding the `DISTINCT` keyword to our query:

~~~ {.sql}
SELECT DISTINCT Publisher FROM Works;
~~~

<table>
<TR><TH>Publisher</TH>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>Faber &amp; Faber</TD>
</TR>
<TR><TD>Belknap Press</TD>
</TR>
<TR><TD>McGraw-Hill</TD>
</TR>
<TR><TD>Peachpit</TD>
</TR>
<TR><TD>South-Western</TD>
</TR>
<TR><TD>Apress</TD>
</TR>
<TR><TD>Sams</TD>
</TR>
</table>

If we select more than one column (for example, both the place and publisher) then the distinct pairs of values are returned:

~~~ {.sql}
SELECT DISTINCT Place, Publisher FROM Works;
~~~

<table>
<TR><TH>Place</TH>
<TH>Publisher</TH>
</TR>
<TR><TD>Sebastopol</TD>
<TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Hoboken</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>London</TD>
<TD>Faber &amp; Faber</TD>
</TR>
<TR><TD>Cambridge</TD>
<TD>Belknap Press</TD>
</TR>
<TR><TD>New York</TD>
<TD>McGraw-Hill</TD>
</TR>
<TR><TD>Indianapolis</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>Berkeley</TD>
<TD>Peachpit</TD>
</TR>
<TR><TD>Mason</TD>
<TD>South-Western</TD>
</TR>
<TR><TD>Berkeley</TD>
<TD>Apress</TD>
</TR>
<TR><TD>Indianapolis</TD>
<TD>Sams</TD>
</TR>
<TR><TD>Cambridge</TD>
<TD>O&#39;Reilly</TD>
</TR>
</table>

Notice in both cases that duplicates are removed even if they didn't appear to be adjacent in the database. Again, it's important to remember that rows aren't actually ordered: they're just displayed that way.


> ## Finding Distinct Years of Item Acquisition {.challenge}
>
> Write a query that displays all the distinct years in which items were purchased for the library. 
> Hint, look at the `Aquired` column in the `Items` table.


As we mentioned earlier, database records are not stored in any particular order. This means that query results aren't necessarily sorted, and even if they are, we often want to sort them in a different way, e.g., in alphabetical order instead of the order in which they were written into the database. We can do this in SQL by adding an `ORDER BY` clause to our query:

~~~ {.sql}
SELECT DISTINCT Publisher FROM Works ORDER BY Publisher;
~~~

<table>
<TR><TH>Publisher</TH>
</TR>
<TR><TD>Apress</TD>
</TR>
<TR><TD>Belknap Press</TD>
</TR>
<TR><TD>Faber &amp; Faber</TD>
</TR>
<TR><TD>McGraw-Hill</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Peachpit</TD>
</TR>
<TR><TD>Sams</TD>
</TR>
<TR><TD>South-Western</TD>
</TR>
<TR><TD>Wiley</TD>
</TR>
</table>


By default, results are sorted in ascending order (i.e., from least to greatest or for A to Z). We can sort in the opposite order using `DESC` (for "descending"):


~~~ {.sql}
SELECT DISTINCT Publisher FROM Works ORDER BY Publisher DESC;
~~~

<table>
<TR><TH>Publisher</TH>
</TR>
<TR><TD>Wiley</TD>
</TR>
<TR><TD>South-Western</TD>
</TR>
<TR><TD>Sams</TD>
</TR>
<TR><TD>Peachpit</TD>
</TR>
<TR><TD>O&#39;Reilly</TD>
</TR>
<TR><TD>McGraw-Hill</TD>
</TR>
<TR><TD>Faber &amp; Faber</TD>
</TR>
<TR><TD>Belknap Press</TD>
</TR>
<TR><TD>Apress</TD>
</TR>
</table>

(And if we want to make it clear that we're sorting in ascending order, we can use `ASC` instead of `DESC`.)

We can also sort on several fields at once. For example, this query sorts the `Works` table first by `Date` (in descending order), then by `Publisher` in ascending order:

~~~ {.sql}
SELECT Title, Date, Publisher FROM Works ORDER BY Date DESC, Publisher ASC;
~~~

<table>
<TR><TH>Title</TH>
<TH>Date</TH>
<TH>Publisher</TH>
</TR>
<TR><TD>Capital in the 21st century</TD>
<TD>2014</TD>
<TD>Belknap Press</TD>
</TR>
<TR><TD>Geek sublime</TD>
<TD>2014</TD>
<TD>Faber &amp; Faber</TD>
</TR>
<TR><TD>PHP &amp; MySQL</TD>
<TD>2013</TD>
<TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Microsoft SQL server 2012</TD>
<TD>2013</TD>
<TD>Sams</TD>
</TR>
<TR><TD>SQL for dummies</TD>
<TD>2013</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>Access 2013 all-in-one</TD>
<TD>2013</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>Discovering SQL</TD>
<TD>2011</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>SQL all-in-one</TD>
<TD>2011</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>Using SQLite</TD>
<TD>2010</TD>
<TD>O&#39;Reilly</TD>
</TR>
<TR><TD>SQL for dummies</TD>
<TD>2010</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>SQL</TD>
<TD>2009</TD>
<TD>McGraw-Hill</TD>
</TR>
<TR><TD>SQL in a nutshell</TD>
<TD>2009</TD>
<TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Learning SQL</TD>
<TD>2009</TD>
<TD>O&#39;Reilly</TD>
</TR>
<TR><TD>Beginning SQL queries</TD>
<TD>2008</TD>
<TD>Apress</TD>
</TR>
<TR><TD>A guide to SQL</TD>
<TD>2008</TD>
<TD>South-Western</TD>
</TR>
<TR><TD>SQL bible</TD>
<TD>2008</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>MySQL in a nutshell</TD>
<TD>2005</TD>
<TD>O&#39;Reilly</TD>
</TR>
<TR><TD>SQL</TD>
<TD>2005</TD>
<TD>Peachpit</TD>
</TR>
<TR><TD>Beginning SQL</TD>
<TD>2005</TD>
<TD>Wiley</TD>
</TR>
<TR><TD>SQL in a nutshell</TD>
<TD>2004</TD>
<TD>O&#39;Reilly</TD>
</TR>
</table>


> ## Displaying Full Names {.challenge}
>
> Write a query that displays the Personal and Family name of the authors in the `Authors` table, ordered by Family name.
