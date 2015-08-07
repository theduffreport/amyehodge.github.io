#Answers to Exercises

####Exercise 1
Write a query that returns The year, month, day, speciesID and weight in mg

```
SELECT year, month, day, species, wgt*1000 
FROM surveys;
```

To double-check yourself:

```
SELECT year, month, day, species, wgt, wgt*1000 
FROM surveys;
```

####Exercise 2
Write a query that returns the day, month, year, species ID, and weight (in kg) for individuals caught on Plot 1 that weigh more than 75 g

```
SELECT day, month, year, species, wgt/1000.0 
FROM surveys 
WHERE (plot=1) and (wgt > 75);
```

To double-check yourself:

```
SELECT day, month, year, species, wgt, plot, wgt/1000.0 
FROM surveys 
WHERE (plot=1) and (wgt > 75);
```

####Exercise 3
Write a query that returns year, species, and weight in kg from the surveys table, sorted with the largest weights at the top

```
SELECT year, species, wgt/1000.0
FROM surveys
ORDER BY wgt/1000.0 DESC
```

####Exercise 4
Let's try to combine what we've learned so far in a single query. Using the surveys table write a query to display the three date fields, species ID, and weight in kilograms (rounded to two decimal places), for rodents captured in 1999, ordered alphabetically by the species ID.

```
SELECT year, month, day, species, ROUND(wgt/1000.0, 2)
FROM surveys
WHERE sex="F" AND year=1999
ORDER BY species ASC;
```

From the surveys table, can we use one query to output the total weight, average weight, and the min and max weights? 

```
SELECT SUM(wgt/1000.0), MAX(wgt/1000.0), MIN(wgt/1000.0), AVG(wgt/1000.0)
FROM surveys;
```

####Exercise 5
Write queries that return

A. How many individuals were counted in each year

```
SELECT year, COUNT(*)
FROM surveys
GROUP BY year;
```

B. Average weight of each species in each year

```
SELECT year, ROUND(AVG(wgt),2)
FROM surveys
GROUP BY year;
```

Example of grouping by multiple fields: 
```
SELECT year, plot, ROUND(AVG(wgt),2)
FROM surveys
GROUP BY year, plot;
```

####Exercise 6
Write a query that returns the genus, the species, and the weight of every individual captured at the site

```
SELECT species.genus, species.species, surveys.wgt
FROM surveys
JOIN species ON surveys.species = species.species_ID;
```

####Exercise 7
Write a query that returns the genus, species, plot type and average weights (rounded to two decimal places) for each species of individual captured, reported by species_ID and plot type and ordered from the lowest weight to the highest. Exclude all records that don't have weight values recorded.

```
SELECT species.genus, species.species, plots.plot_type, ROUND(AVG(surveys.wgt), 2)
FROM surveys
JOIN species ON surveys.species = species.species_ID
JOIN plots ON plots.plot_ID = surveys.plot
WHERE surveys.wgt > 0
GROUP BY plots.plot_type, species.species_id
ORDER BY AVG(surveys.wgt);
```

####Exercise 8
Using a SET OPERATOR (EXCEPT) all the species found in 2000 but not in 2002, report back species

```
SELECT DISTINCT species.genus, species.species, species.species_ID
FROM surveys
JOIN species ON surveys.species = species.species_ID
WHERE surveys.year=1977
EXCEPT
SELECT DISTINCT species.genus, species.species, species.species_ID
FROM surveys
JOIN species ON surveys.species = species.species_ID
WHERE surveys.year = 2002;
```
