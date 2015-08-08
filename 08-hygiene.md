---
layout: page
title: Databases and SQL
subtitle: Data Hygiene
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Explain what an atomic value is.
> *   Distinguish between atomic and non-atomic values.
> *   Explain why every value in a database should be atomic.
> *   Explain what a primary key is and why every record should have one.
> *   Identify primary keys in database tables.
> *   Explain why database entries should not contain redundant information.
> *   Identify redundant information in databases.

Now that we have seen how joins work,
we can see why the relational model is so useful
and how best to use it.
The first rule is that every value should be [atomic](reference.html#atomic),
i.e.,
not contain parts that we might want to work with separately.
We store personal and family names in separate columns instead of putting the entire name in one column
so that we don't have to use substring operations to get the name's components.
More importantly,
we store the two parts of the name separately because splitting on spaces is unreliable:
just think of a name like "Eloise St. Cyr" or "Jan Mikkel Steubart".

The second rule is that every record should have a unique primary key.
This can be a serial number that has no intrinsic meaning
(like the `work_ID` field in the `Works` table),
one of the values in the record (the `barcode` field in the `Items` table could have been
used instead of the `item_ID`),
or even a combination of values.

The third rule is that there should be no redundant information.
For example, we could have a single table combining the data from the
`Items` and the `Works` tables, like this:

|Item_ID|Barcode     |Acquired|Status  |Title            |ISBN         |Date|Place     |Publisher|Edition|Pages|
|-------|------------|--------|--------|-----------------|-------------|----|----------|---------|-------|-----|
|1      |081722942611|2009    |Loaned  |SQL in a nutshell|9780596518844|2009|Sebastopol|O'Reilly |3rd ed.|578  |
|2      |492437609065|2011    |On shelf|SQL in a nutshell|9780596518844|2009|Sebastopol|O'Reilly |3rd ed.|578  |
|3      |172480710952|2013    |On shelf|SQL for dummies  |9781118607961|2013|Hoboken   |Wiley    |8th ed.|     |
|4      |708014968732|2013    |Missing |PHP & MySQL      |9781449325572|2013|Sebastopol|O'Reilly |2nd ed.|532  |
|5      |819783404942|2014    |Loaned  |PHP & MySQL      |9781449325572|2013|Sebastopol|O'Reilly |2nd ed.|532  |


In fact,
we could use a single table that recorded all the information about each item in each row,
just as a spreadsheet would,
including the author information in individual columns.
The problem is that it's very hard to keep data organized this way consistent:
if we realize that the bibliographic information for a particular title is wrong,
we have to change all the item records for this title.
Storing the author/contributor information would pose another challenge still:
our spreasheet would need to have as many columns to store this information so as to
fit the records with the greatest number of contributors in our database. Those columns
would mostly be empty for records that have a smaller number of contributors. What's worse,
if we added a title with an even greater number of contributors to the database, we
would need to alter our table to add even more columns to it.

The fourth rule is that the units for every value should be stored explicitly.
If alongside the number of pages we were also recording the size of each item in
our library, we would need to specify whether that size is expressed in inches
or centimeters, for example.

Stepping back,
data and the tools used to store it have a symbiotic relationship:
we use tables and joins because it's efficient,
provided our data is organized a certain way,
but organize our data that way because we have tools to manipulate it efficiently.
As anthropologists say,
the tool shapes the hand that shapes the tool.

> ## Identifying Atomic Values {.challenge}
>
> Which of the following are atomic values? Which are not? Why?
>
> *   New Zealand
> *   87 Turing Avenue
> *   January 25, 1971
> *   the XY coordinate (0.5, 3.3)

> ## Identifying a Primary Key {.challenge}
>
> What is the primary key in this table?
> I.e., what value or combination of values uniquely identifies a record?
>
> |Work_ID|Title                    |ISBN         |Date|Place       |Publisher|
> |-------|-------------------------|-------------|----|------------|---------|
> |16     |Microsoft SQL server 2012|9780132977661|2013|Indianapolis|Sams     |