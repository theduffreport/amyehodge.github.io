#Workshop: Beginning SQL

This workshop will teach the basics of working with and querying structured data in a database environment. This workshop uses the SQLite plugin for Firefox. We will provide laptops with the correct software for the workshop. If you bring your own machine to the workshop, you must have the software installed in advance. 

## Software and Data Setup

> *If you are using one of the Library's laptops, skip this section.*

1. [Download Firefox] (https://www.mozilla.org/en-US/firefox/new/) and install it.
2. Download and install the SQLite Manager add on: **Menu (the three horizontal lines near the
top right corner of Firefox) -> Add-ons -> Search -> SQLite
Manager -> Install -> Restart now**
4. Add SQLite Manager to the menu: **Menu -> Customize, then drag the SQLite Manager icon to one of the empty menu squares on the right, Exit Customize**
3. Download the [Portal Database](http://files.figshare.com/1919743/portal_mammals.sqlite) as well as the individual csv files for [plots](http://files.figshare.com/1919738/plots.csv), [species](http://files.figshare.com/1919742/species.csv), and [surveys](http://files.figshare.com/1919744/surveys.csv).
5. Open SQLite Manager: **Menu -> SQLite Manager**

## Dataset Description

The data we will be using is a time-series for a small mammal community in
southern Arizona. This is part of a project studying the effects of rodents and
ants on the plant community that has been running for almost 40 years. The
rodents are sampled on a series of 24 plots, with different experimental
manipulations controlling which rodents are allowed to access which plots.

This is a real dataset that has been used in over 100 publications. We've
simplified it a little bit for the workshop, but you can download the
[full dataset](http://esapubs.org/archive/ecol/E090/118/) and work with it using
exactly the same tools we'll learn about today.

## Import Data into SQLite

We are going to start by importing the data tables that we want to use in our database.

1. Start a New Database **Database -> New Database**
2. Name your database and save it to the desktop
3. Import your data tables into your database by selecting **Database -> Import**
4. Click on the **Select File** and choose the csv file you want to import
5. Give the table a name (or use the default)
6. If the the file has column headings in the first row, check the appropriate box (our tables have column headings in the first row)
7. Make sure the delimiter and quotation options are correct (our tables use a comma delimiter and double quotes)

![Setting import options](http://amyehodge.github.io/Beginning_SQL/images/BSQL1.png "Setting import options")  

8. Press **OK**

When asked if you want to modify the table, click **OK** and then set the data types for each column (field) as indicated below.

#####Data types for each column (field)

| Table | Column | Data Type | Other features |
| ------|------|------|------ |
| plots | plot_id |integer | primary key, unique |
| plots | plot_type | varchar |


![Defining data types](http://amyehodge.github.io/Beginning_SQL/images/BSQL2.png "Defining data types")

Once the data has been imported, the panel on the left will display a list of the tables under the heading "Tables."

To see the contents of a table, click on that table and then click on the Browse
and search tab in the right hand section of the screen.

![Contents of species table](http://amyehodge.github.io/Beginning_SQL/images/BSQL3.png "Contents of species table")

***EXERCISE 0: Import the species and surveys tables using the information provided in the table below.***

| Table | Column | Data Type | Other features |
| :------|:------|:------|:------ |
| species | species_id | char(2) | primary key, unique |
| species | genus | varchar |  |
| species | species | varchar | 	 |
| species | taxa | varchar |  |
| surveys | record_id | integer | primary key, unique |
| surveys | month | integer |  |
| surveys | day | integer |  |
| surveys | year | 	integer	 |  |
| surveys | plot_id | integer |  |
| surveys | species_id | char(2) | allow null  |
| surveys | sex | char(1) | allow null | 
| surveys | hindfoot_length | integer | allow null |
| surveys | weight | integer | allow null |

You can also use this same approach to append new data to an existing table.

##<a name="design"></a>Relational databases and database design

* Relational databases store data in tables with fields (columns) and records (rows).
* The records are not in any particular order.
* Data fields have types and all values in a field have the same type ([list of data types](#datatypes)). We defined these data types when we imported our tables.
* Each table contains a single field (or combination of fields) that uniquely defines each database record. This field is called the primary key. We defined these as well when we imported our tables.
* Every row-column combination (cell) contains a single *atomic* value, i.e., not containing parts we might want to work with separately. Note how the genus and species information in the species table are in separate fields.

![Row-column combinations contain atomic values](http://amyehodge.github.io/Beginning_SQL/images/BSQL4.png "Row-column combinations contain atomic value")

* Data are split into multiple tables, each containing one class of information.
* Tables are related by shared columns of information. The field of data in one table that correlates to the primary key field of another table is called the foreign key.
* Queries let us look up data or make calculations based on the fields of data.


## Why use relational databases

* Data separate from analysis.
  * There is no risk of accidentally changing data when analyzing it.
  * If we change the data we can rerun the query
* It's fast for large amounts of data.
* It improves quality control of data entry: it's possible to constrain types, which is especially easy if you use forms available in database programs like Access, Filemaker, etc.

## Database Management Systems

There are a number of different database management systems for working with relational data. We're going to use SQLite today, but basically everything we teach you will apply to the other database systems as well (e.g., MySQL, PostgreSQL, MS Access, Filemaker Pro). The differences between these are mostly in the details of exactly how to import and export data, the specific data types, and nuances of how the queries are written.


## Basic queries
To write a query, click on the Execute SQL tab from within any of your database tables.

![Execute SQL](http://amyehodge.github.io/Beginning_SQL/images/BSQL5.png "Execute SQL")  
    
Let's start by writing a query to pull up information from the **surveys** table.
Here we have data on every individual that was captured at the site, including when they were captured, what plot they were captured on, their species ID, sex, hindfoot length, and weight in grams.

Let’s write a SQL query that selects only the year column from the **surveys** table. Enter the query in the box labeled "Enter SQL."

    SELECT year FROM surveys;

This is called a "SELECT statement." We have capitalized the words `SELECT` and `FROM` because they are SQL keywords. SQL is case insensitive, but capitalizing helps with readability and is considered a best practice for SQL. In addition, every query must end in a semicolon. This is how the software knows this is the end of the query.

To run the query in SQLite, click on the RunSQL button that is underneath the text box. If you were doing this at a command-line interface, you would hit "enter."

![Run query](http://amyehodge.github.io/Beginning_SQL/images/BSQL6.png "Run query") 

If we want more information, we can add a new column to the list of fields that are listed immediately after `SELECT`.

    SELECT year, month, day FROM surveys;

Or we can select all of the columns in a table using the wildcard "``*``".

    SELECT * FROM surveys;

### Unique values

If we want only the unique values so that we can quickly see what species have been sampled we use the keyword ``DISTINCT``.

    SELECT DISTINCT species_id FROM surveys;

If we select more than one column, then the distinct pairs of values are returned.

    SELECT DISTINCT year, species_id FROM surveys;
    
This is a good point to introduce another best practice for formatting SQL queries. It helps with readability if you separate the parts of the query onto separate lines. So the above query should be written:

    SELECT DISTINCT year, species_id
    FROM surveys;

### Calculated values

We can also do calculations with the values in a query. For example, if we wanted to look at the mass of each individual on different dates, but we needed it in kg instead of g we could use this query:

    SELECT year, month, day, weight/1000.0
    FROM surveys;

When we run the query, the expression `weight/1000.0` is evaluated for each row and appended to that row, in a new column. Note that because weight is an integer, if we divide by the integer 1000, the results will be reported as integers. In order to get more significant digits, you need to include the decimal point so that SQL knows you want the results reported as floating point numbers. 

Expressions can use any fields, any arithmetic operators (+ - * /) and a variety of built-in functions (). For example, we could round the values to make them easier to read.

    SELECT plot_id, species_ID, sex, weight, ROUND(weight/1000.0, 2)
    FROM surveys;

***EXERCISE 1: Write a query that returns the year, month, day, species ID, and weight in mg***

## Filtering

Databases can also filter data – selecting only the data meeting certain criteria. For example, let’s say we only want data for the species [Dipodomys merriami](https://en.wikipedia.org/wiki/Merriam%27s_kangaroo_rat), which has a species code of DM. We need to add a `WHERE` clause to our query:

    SELECT * 
    FROM surveys 
    WHERE species_id="DM";

We can do the same thing with numbers. Here, we only want the data since 2000:

    SELECT * 
    FROM surveys 
    WHERE year >= 2000;

We can use more sophisticated conditions by combining filters with AND as well as OR. For example, suppose we want the data on Dipodomys merriami starting in the year 2000:

    SELECT * 
    FROM surveys 
    WHERE (year >= 2000) AND (species_id = "DM");

Note that the parentheses aren’t needed in this case, but again, they help with readability. They also ensure that the computer combines `AND` and `O`R in the way that we intend. (`AND` takes precedence over `OR` and will be evaluated before `OR`.)

If we wanted to get data for any of the Dipodomys species, which have species codes DM, DO, and DS, we could combine the tests using OR:

    SELECT * 
    FROM surveys 
    WHERE (species_id = "DM") OR (species_id = "DO") OR (species_id = "DS");

***EXERCISE 2: Write a query that returns the day, month, year, species ID, and weight (in kg) for individuals caught on Plot 1 that weigh more than 75 g***


## Exporting & saving query results

* Export Results:  To export your results into a saved file on your computer, click on the button next to **Actions** and choose **Save Result to File**. This file is not queriable.
* Save Results: To save your results as a special kind of table called a view, click on **View** in the main menu and choose **Create View**. You can view and query this special table in SQLite.


## Building more complex queries

Now, let's combine the above queries to get data for the three Dipodomys species starting from the year 2000. This time, let’s use `IN` as one way to make the query easier to understand. `IN` is equivalent to saying `WHERE (species_id = "DM") OR (species_id = "DO") OR (species_id = "DS")`, but reads more neatly:

    SELECT * 
    FROM surveys 
    WHERE (year >= 2000) AND (species_id IN ("DM", "DO", "DS"));

We started with something simple, then added more clauses one by one, testing their effects as we went along. For complex queries, this is a good strategy to make sure you are getting what you want. It also might be helpful to create a subset of the data that you can easily see in a temporary database to practice your queries on before working on a larger or more complicated database.


## Sorting

We can also sort the results of our queries by using `ORDER BY`. For simplicity, let’s go back to the **species** table and alphabetize it by taxa.

    SELECT * 
    FROM species 
    ORDER BY taxa ASC;

The keyword `AS`C tells us to order it in Ascending order. We could alternately use `DESC` to get descending order.

    SELECT * 
    FROM species 
    ORDER BY taxa DESC;

ASC is the default.

We can also sort on several fields at once. To truly be alphabetical, we might want to order by genus then species.

    SELECT * 
    FROM species 
    ORDER BY genus ASC, species ASC;

***Exercise 3: Write a query that returns year, species, and weight in kg from the surveys table, sorted with the largest weights at the top***

##Missing data

`NULL` can be used in queries to represent missing data (note that this is not the same as true or false or 0).

For example, to find all instances where the species_id was not entered, we can write this query:

    SELECT * 
    FROM surveys 
    WHERE species_id IS NULL;

Or to find all cases where a weight value was entered:

    SELECT * 
    FROM surveys 
    WHERE weight IS NOT NULL;

##Order of execution vs. order of writing

Another note for ordering. We don’t actually have to display a column to sort by it. For example, let’s say we want to order by the species ID, but we only want to see genus and species.

    SELECT genus, species
    FROM species
    ORDER BY species_id ASC;

We can do this because sorting occurs earlier in the computational pipeline than field selection.

The computer is basically doing this:

1. Collecting data from tables according to `FROM`
2. Filtering rows according to `WHERE`
3. Sorting results according to `ORDER BY`
4. Displaying requested columns or expressions according to `SELECT`


When we write queries, SQL dictates the query parts be supplied in a particular order: `SELECT`, `FROM`, `JOIN...ON`, `WHERE`, `GROUP BY`, `ORDER BY`. Note that this is not the same order in which the query is executed. (We'll get to `JOIN...ON` and `GROUP BY` in a bit.)


***Exercise 4: Let's try to combine what we've learned so far in a single query.
Using the surveys table write a query to display the three date fields, species ID, and weight in kilograms (rounded to two decimal places), for rodents captured in 1999, ordered alphabetically by the species ID.***


## Aggregation

Aggregation allows us to combine results by grouping records based on value and to calculate combined values in groups.

Let’s go to the **surveys** table and find out how many individuals there are. Using the wildcard simply counts the number of records (rows).

    SELECT COUNT(*)
    FROM surveys;

We can also find out how much all of those individuals weigh by using the function `SUM`.

    SELECT COUNT(*), SUM(weight)
    FROM surveys;

Do you think you could output this value in kilograms, rounded to 3 decimal places?

    SELECT ROUND(SUM(weight)/1000.0, 3)
    FROM surveys;

There are many other aggregate functions included in SQL including `MAX`, `MIN`, and `AVG`.

***Exercise 5: From the surveys table, can we use one query to output the total weight, average weight, and the min and max weights?***

Now, let's see how many individuals were counted in each species. We do this using a `GROUP BY` clause.

    SELECT species_id, COUNT(*)
    FROM surveys
    GROUP BY species_id;

`GROUP BY` tells SQL what field or fields we want to use to aggregate the data. If we want to group by multiple fields, we give `GROUP BY` a comma separated list.

***EXERCISE 6: Write queries that return: 1. How many individuals were counted in each year. 2. Average weight of each species in each year***

We can order the results of our aggregation by a specific column, including the aggregated column. Let’s count the number of individuals of each species captured, ordered by the count.

    SELECT species_id, COUNT(*)
    FROM surveys
    GROUP BY species_id
    ORDER BY COUNT(species_id);


## Joins

To combine data from two tables we use the SQL `JOIN` command, which comes after the `FROM` command.

We will also need to use the keyword `ON` to tell the computer which columns provide the link ([Primary Key > Foreign Key](#design)) between the two tables. In this case, the species_id column in the **species** table is defined as the primary key. It contains the same data as the survey table's species_id column, which is the foreign key in this case.  We want to join the tables on these species_id fields.

    SELECT *
    FROM surveys
    JOIN species ON surveys.species_id = species.species_id;

`ON` is kind of like `WHERE`, in that it filters things out according to a test condition. We use the table.colname format to tell the software what column in which table we are referring to.

We often won't want all of the fields from both tables, so anywhere we would have used a field name in a non-join query, we can use *table.colname*.

For example, what if we wanted information on when individuals of each species were captured, but instead of their species ID we wanted their actual species names.

    SELECT surveys.year, surveys.month, surveys.day, species.genus, species.species
    FROM surveys
    JOIN species ON surveys.species_id = species.species_id;

***Exercise 7: Write a query that returns the genus, the species, and the weight of every individual captured at the site***

Joins can be combined with sorting, filtering, and aggregation. So, if we wanted average weight of the individuals on each different type of treatment, we could do something like:

    SELECT plots.plot_type, AVG(surveys.weight)
    FROM surveys
    JOIN plots
    ON surveys.plot_id = plots.plot_id
    GROUP BY plots.plot_type;

You can also combine many tables using a join. The query must include enough `JOIN`...`ON` clauses to link all of the tables together. All of the data in the three tables in our database can be displayed together using this query:

    SELECT *
    FROM surveys
    JOIN plots ON surveys.plot_id = plots.plot_id
    JOIN species ON species.species_id = surveys.species_id;

***Exercise 8: Write a query that returns the genus, species, plot type and average weights (rounded to two decimal places) for each species of individual captured, reported by species_ID and plot type and ordered from the lowest weight to the highest. Exclude all records that don't have weight values recorded.

## Set operators

The set operators `UNION`, `UNION ALL`, `INTERSECT`, and `EXCEPT` can be used to compare the results from two `SELECT` statements. Note that the fields selected in the two queries must be identical in order for this to work. 

    A UNION B will return all the distinct rows present to A & B
    A ALL B will return all the rows present to A & B, including repetitions
    A INTERSECT B will return all the rows common to A & B
    A EXCEPT B will return all the rows present in A but not present in B

To use a set operator, write the two queries and combine them with the operator. For example, this query will identify all the individuals that weight over 50g and that were female. Note that you could more easily and elegantly write this query without the set operator; this is only for example purposes.

    SELECT species_id, plot_id, sex, weight 
    FROM surveys
    WHERE sex='F'
    INTERSECT
    SELECT species_id, plot_id, sex, weight
    FROM surveys
    WHERE weight>225;
    
***Exercise 9: Write a query to identify all the species (by genus, species, and species_id) found in 1977 but not in 2002.

## Adding data to existing tables

* Browse & Search -> Add
* Enter data into a csv file and append


## Other database management systems

* Access or Filemaker Pro
    * GUI
    * Forms w/QAQC
	* But not cross-platform
* MySQL/PostgreSQL
    * Multiple simultaneous users
	* More difficult to setup and maintain


##<a name="datatypes"></a> Data types

The following table shows some common SQL data types. Different database platforms may have slightly different data types. 


| Data type  | Description |
| :------------- | :------------- |
| CHARACTER(n)  | Character string. Fixed-length n  |
| VARCHAR(n) or CHARACTER VARYING(n) |	Character string. Variable length. Maximum length n |
| BINARY(n) |	Binary string (0s and 1s). Fixed-length n |
| BOOLEAN	| Stores TRUE or FALSE values |
| VARBINARY(n) or BINARY VARYING(n) |	Binary string. Variable length. Maximum length n |
| INTEGER(p) |	Integer numerical (no decimal). Precision p.|
| SMALLINT | 	Integer numerical (no decimal). Precision 5.|
| INTEGER |	Integer numerical (no decimal). Precision 10.|
| BIGINT |	Integer numerical (no decimal). Precision 19.|
| DECIMAL(p,s) |	Exact numerical, precision p, scale s. Example: decimal(5,2) is a number that has 3 digits before the decimal and 2 digits after the decimal|
| NUMERIC(p,s) |	Exact numerical, precision p, scale s. (Same as DECIMAL) |
| FLOAT(p) |	Approximate numerical, mantissa precision p. A floating number in base 10 exponential notation. The size argument for this type consists of a single number specifying the minimum precision.|
| REAL |	Approximate numerical, mantissa precision 7. |
| FLOAT |	Approximate numerical, mantissa precision 16. |
| DOUBLE PRECISION |	Approximate numerical, mantissa precision 16. |
| DATE |	Stores year, month, and day values |
| TIME |	Stores hour, minute, and second values |
| TIMESTAMP |	Stores year, month, day, hour, minute, and second values |
| INTERVAL |	Composed of a number of integer fields, representing a period of time, depending on the type of interval |
| ARRAY |	A set-length and ordered collection of elements |
| MULTISET | 	A variable-length and unordered collection of elements |
| XML |	Stores XML data |


Table source: [W3 Schools](http://www.w3schools.com/sql/sql_datatypes_general.asp)

## Resources
[SQL Cheat Sheet](http://amyehodge.github.io/Beginning_SQL/SQL_cheat_sheet.md)
[Exercise Answer Key](http://amyehodge.github.io/Beginning_SQL/exercised_key.md)
List some books available at the library 
### Online tutorials
*[W3 Schools](http://www.w3schools.com/sql/)
*[SQLZOO](http://sqlzoo.net/)
*lynda.com training (free to everyone at Stanford!)
**[Relational Database Fundamentals](http://www.lynda.com/Access-tutorials/Relational-Database-Fundamentals/145932-2.html)
**[SQL Essential Training](http://www.lynda.com/SQL-tutorials/SQL-Essential-Training/139988-2.html)
**[MySQL Essential Training](http://www.lynda.com/MySQL-tutorials/MySQL-Essential-Training/139986-2.html)
**[Up and Running with MySQL Development](http://www.lynda.com/MySQL-tutorials/Up-Running-MySQL-Development/158373-2.html)
**[Foundations of Programming: Databases](http://www.lynda.com/Programming-tutorials/Foundations-Programming-Databases/112585-2.html)
[TutorialsPoint](http://www.tutorialspoint.com/sqlite/index.htm)
Or try some of the tutorials listed [here](http://www.vertabelo.com/blog/notes-from-the-lab/18-best-online-resources-for-learning-sql-and-database).
