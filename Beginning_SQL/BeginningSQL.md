#Workshop: Beginning SQL

This workshop will teach the basics of working with and querying structured data in a database environment. This workshop uses the SQLite plugin for Firefox. We will provide laptops with the correct software for the workshop. If you bring your own machine to the workshop, you must have the software installed in advance. 

##Software and Data Setup

> *If you are using one of the Library's laptops, skip this section.*

1. [Download Firefox] (https://www.mozilla.org/en-US/firefox/new/) and install it.
2. Download and install the SQLite Manager add on: **Menu (the three horizontal lines near the
top right corner of Firefox) -> Add-ons -> Search -> SQLite
Manager -> Install -> Restart now**
4. Add SQLite Manager to the menu: **Menu -> Customize, then drag the SQLite Manager icon to one of the empty menu squares on the right, Exit Customize**
3. Download the [Portal Database](http://files.figshare.com/1919743/portal_mammals.sqlite) as well as the individual csv files for [plots](http://files.figshare.com/1919738/plots.csv), [species](http://files.figshare.com/1919742/species.csv), and [surveys](http://files.figshare.com/1919744/surveys.csv).
5. Open SQLite Manager: **Menu -> SQLite Manager**

##Dataset Description

The data we will be using is a time-series for a small mammal community in
southern Arizona. This is part of a project studying the effects of rodents and
ants on the plant community that has been running for almost 40 years. The
rodents are sampled on a series of 24 plots, with different experimental
manipulations controlling which rodents are allowed to access which plots.

This is a real dataset that has been used in over 100 publications. We've
simplified it just a little bit for the workshop, but you can download the
[full dataset](http://esapubs.org/archive/ecol/E090/118/) and work with it using
exactly the same tools we'll learn about today.

##Import Data into SQLite

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
| :------|:------|:------|:------ |
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

##Relational databases and database design

* Relational databases store data in tables with fields (columns) and records (rows)
* The records are not in any particular order
* Data fields have types and all values in a field have the same type ([list of data types](#datatypes)). We defined these data types when we imported our tables.
* Each table contains a single field (or combination of fields) that uniquely defines each database record. This field is called the primary key. We defined these as well when we imported our tables.
* Every row-column combination (cell) contains a single *atomic* value, i.e., not containing parts we might want to work with separately. Note how the genus and species information in the species table are in separate fields.

    ![Row-column combinations contain atomic values](http://amyehodge.github.io/Beginning_SQL/images/BSQL4.png "Row-column combinations contain atomic value")

* Data are split into multiple tables, each containing one class of information. 
* Tables are related by shared columns of information. The field of data in one table that correlates to the primary key field of another table is called the foreign key.
* Queries let us look up data or make calculations based on the fields of data.


##Why use relational databases

* Data separate from analysis.
  * No risk of accidentally changing data when analyzing it
  * If we change the data we can just rerun the query
* Fast for large amounts of data
* Improve quality control of data entry: it's possible to constrain types, which is especially easy if you use forms available in database programs like Access, Filemaker, etc.)

##Database Management Systems

There are a number of different database management systems for working with relational data. We're going to use SQLite today, but basically everything we teach you will apply to the other database systems as well (e.g., MySQL, PostgreSQL, MS Access, Filemaker Pro). The differences between these are mostly in the details of exactly how to import and export data, the specific data types, and nuances of how the queries are written.


##Basic queries
To write a query, click on the Execute SQL tab from within any of your database tables.

![Execute SQL](http://amyehodge.github.io/Beginning_SQL/images/BSQL5.png "Execute SQL")  
    
Let's start by writing a query to pull up information from the **surveys** table.
Here we have data on every individual that was captured at the site, including when they were captured, what plot they were captured on, their species ID, sex, and weight in grams.

Let’s write a SQL query that selects only the year column from the surveys table.

    SELECT year FROM surveys;

We have capitalized the words SELECT and FROM because they are SQL keywords. SQL is case insensitive, but it helps for readability – good style.

If we want more information, we can just add a new column to the list of fields, right after SELECT:

    SELECT year, month, day FROM surveys;

Or we can select all of the columns in a table using the wildcard *

    SELECT * FROM surveys;

### Unique values

If we want only the unique values so that we can quickly see what species have been sampled we use ``DISTINCT``

    SELECT DISTINCT species FROM surveys;

If we select more than one column, then the distinct pairs of values are returned

    SELECT DISTINCT year, species_id FROM surveys;

### Calculated values

We can also do calculations with the values in a query. For example, if we wanted to look at the mass of each individual on different dates, but we needed it in kg instead of g we would use

    SELECT year, month, day, weight/1000.0 from surveys;

When we run the query, the expression `weight / 1000.0` is evaluated for each row and appended to that row, in a new column. Note that because weight is an integer, if we divide by the integer 1000, the results will be reported as integers. In order to get more significant digits, you need to include the decimal point so that SQL knows you want the results reported as floating point numbers. 

Expressions can use any fields, any arithmetic operators (+ - * /) and a variety of built-in functions (). For example, we could round the values to make them easier to read.

    SELECT plot, species_ID, sex, weight, ROUND(weight / 1000.0, 2) FROM surveys;

***EXERCISE: Write a query that returns the year, month, day, speciesID and weight in mg***

##Filtering

Databases can also filter data – selecting only the data meeting certain criteria. For example, let’s say we only want data for the species Dipodomys merriami, which has a species code of DM. We need to add a WHERE clause to our query:

    SELECT * FROM surveys WHERE species_id="DM";

We can do the same thing with numbers. Here, we only want the data since 2000:

    SELECT * FROM surveys WHERE year >= 2000;

We can use more sophisticated conditions by combining tests with AND and OR. For example, suppose we want the data on Dipodomys merriami starting in the year 2000:

    SELECT * FROM surveys WHERE (year >= 2000) AND (species_id = "DM");

Note that the parentheses aren’t needed, but again, they help with readability. They also ensure that the computer combines AND and OR in the way that we intend.

If we wanted to get data for any of the Dipodomys species, which have species codes DM, DO, and DS we could combine the tests using OR:

    SELECT * FROM surveys WHERE (species_id = "DM") OR (species_id = "DO") OR (species_id = "DS");

***EXERCISE: Write a query that returns the day, month, year, species ID, and weight (in kg) for individuals caught on Plot 1 that weigh more than 75 g***


##Saving & Exporting queries

* Exporting:  **Actions** button and choosing **Save Result to File**.
* Save: **View** drop down and **Create View**


##Building more complex queries

Now, lets combine the above queries to get data for the 3 Dipodomys species from the year 2000 on. This time, let’s use IN as one way to make the query easier to understand. It is equivalent to saying `WHERE (species_id = "DM") OR (species_id
= "DO") OR (species_id = "DS")`, but reads more neatly:

    SELECT * FROM surveys WHERE (year >= 2000) AND (species_id IN ("DM", "DO", "DS"));

    SELECT *
    FROM surveys
    WHERE (year >= 2000) AND (species_id IN ("DM", "DO", "DS"));

We started with something simple, then added more clauses one by one, testing their effects as we went along. For complex queries, this is a good strategy, to make sure you are getting what you want. Sometimes it might help to take a subset of the data that you can easily see in a temporary database to practice your queries on before working on a larger or more complicated database.


##Sorting

We can also sort the results of our queries by using ORDER BY. For simplicity, let’s go back to the species table and alphabetize it by taxa.

    SELECT * FROM species ORDER BY taxa ASC;

The keyword ASC tells us to order it in Ascending order. We could alternately use DESC to get descending order.

    SELECT * FROM species ORDER BY taxa DESC;

ASC is the default.

We can also sort on several fields at once. To truly be alphabetical, we might want to order by genus then species.

    SELECT * FROM species ORDER BY genus ASC, species ASC;

***Exercise: Write a query that returns year, species, and weight in kg from the surveys table, sorted with the largest weights at the top***


##Order of execution

Another note for ordering. We don’t actually have to display a column to sort by it. For example, let’s say we want to order by the species ID, but we only want to see genus and species.

    SELECT genus, species FROM species ORDER BY taxa ASC;

We can do this because sorting occurs earlier in the computational pipeline than field selection.

The computer is basically doing this:

1. Filtering rows according to WHERE
2. Sorting results according to ORDER BY
3. Displaying requested columns or expressions.


##Order of clauses

The order of the clauses when we write a query is dictated by SQL: SELECT, FROM, WHERE, ORDER BY and we often write each of them on their own line for readability.


***Exercise: Let's try to combine what we've learned so far in a single query.
Using the surveys table write a query to display the three date fields, species ID, and weight in kilograms (rounded to two decimal places), for rodents captured in 1999, ordered alphabetically by the species ID.***


##Aggregation

Aggregation allows us to combine results by grouping records based on value and calculating combined values in groups.

Let’s go to the surveys table and find out how many individuals there are. Using the wildcard simply counts the number of records (rows)

    SELECT COUNT(*) FROM surveys;

We can also find out how much all of those individuals weigh.

    SELECT COUNT(*), SUM(weight) FROM surveys;

***Do you think you could output this value in kilograms, rounded to 3 decimal places?***

    SELECT ROUND(SUM(weight)/1000.0, 3) FROM surveys;

There are many other aggregate functions included in SQL including MAX, MIN, and AVG.

***From the surveys table, can we use one query to output the total weight, average weight, and the min and max weights? How about the range of weight?***

Now, let's see how many individuals were counted in each species. We do this using a GROUP BY clause

    SELECT species_id, COUNT(*)
    FROM surveys
    GROUP BY species_id;

GROUP BY tells SQL what field or fields we want to use to aggregate the data. If we want to group by multiple fields, we give GROUP BY a comma separated list.

***EXERCISE: Write queries that return: 1. How many individuals were counted in each year. 2. Average weight of each species in each year***

We can order the results of our aggregation by a specific column, including the aggregated column. Let’s count the number of individuals of each species captured, ordered by the count

    SELECT species_id, COUNT(*)
    FROM surveys
    GROUP BY species_id
    ORDER BY COUNT(species_id);


##Joins

To combine data from two tables we use the SQL JOIN command, which comes after the FROM command.

We also need to tell the computer which columns provide the link between the two tables using the word ON. What we want is to join the data with the same species codes.

    SELECT *
    FROM surveys
    JOIN species ON surveys.species_id = species.species_id;

ON is like WHERE, it filters things out according to a test condition. We use the table.colname format to tell the manager what column in which table we are referring to.

We often won't want all of the fields from both tables, so anywhere we would have used a field name in a non-join query, we can use *table.colname*

For example, what if we wanted information on when individuals of each species were captured, but instead of their species ID we wanted their actual species names.

    SELECT surveys.year, surveys.month, surveys.day, species.genus, species.species
    FROM surveys
    JOIN species ON surveys.species_id = species.species_id;

***Exercise: Write a query that returns the genus, the species, and the weight of every individual captured at the site***

Joins can be combined with sorting, filtering, and aggregation. So, if we wanted average mass of the individuals on each different type of treatment, we could do something like

    SELECT plots.plot_type, AVG(surveys.wgt)
    FROM surveys
    JOIN plots
    ON surveys.plot = plots.plot_id
    GROUP BY plots.plot_type;


##Adding data to existing tables

* Browse & Search -> Add
* Enter data into a csv file and append


##Other database management systems

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

Source: [W3 Schools](http://www.w3schools.com/sql/sql_datatypes_general.asp)

##Resources
[SQL Cheat Sheet](http://amyehodge.github.io/Beginning_SQL/SQL_cheat_sheet.md)
List some books available at the library 
Lists tutorials online, including codeacademy and lynda.com
[SQLite tutorial[(http://www.tutorialspoint.com/sqlite/index.htm) on TutorialsPoint
