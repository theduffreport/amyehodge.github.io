#Answers to Exercises

####Exercise 1
Write a query that returns the year, month, day, species ID, and weight in mg

```
SELECT year, month, day, species_id, weight*1000 
FROM surveys;
```

To double-check yourself:

```
SELECT year, month, day, species_id, weight, weight*1000 
FROM surveys;
```

####Exercise 2
Write a query that returns the day, month, year, species ID, and weight (in kg) for individuals caught on Plot 1 that weigh more than 75 g

```
SELECT day, month, year, species_id, weight/1000.0 
FROM surveys 
WHERE (plot_id=1) and (weight > 75);
```

To double-check yourself:

```
SELECT day, month, year, species_id, weight, plot_id, weight/1000.0 
FROM surveys 
WHERE (plot_id=1) and (weight > 75);
```

####Exercise 3
Write a query that returns year, species, and weight in kg from the surveys table, sorted with the largest weights at the top

```
SELECT year, species_id, weight/1000.0
FROM surveys
ORDER BY weight/1000.0 DESC;
```

####Exercise 4
Let's try to combine what we've learned so far in a single query. Using the surveys table write a query to display the three date fields, species ID, and weight in kilograms (rounded to two decimal places), for rodents captured in 1999, ordered alphabetically by the species ID.

```
SELECT year, month, day, species_id, ROUND(weight/1000.0, 2)
FROM surveys
WHERE sex="F" AND year=1999
ORDER BY species ASC;
```

#### Exercise 5
From the surveys table, can we use one query to output the total weight, average weight, and the min and max weights? 

```
SELECT SUM(weight/1000.0), MAX(weight/1000.0), MIN(weight/1000.0), AVG(weight/1000.0)
FROM surveys;
```

####Exercise 6
Write queries that return

A. How many individuals were counted in each year

```
SELECT year, COUNT(*)
FROM surveys
GROUP BY year;
```

B. Average weight of each species in each year

```
SELECT year, ROUND(AVG(weight),2)
FROM surveys
GROUP BY year;
```

Example of grouping by multiple fields: 
```
SELECT year, plot_id, ROUND(AVG(weight),2)
FROM surveys
GROUP BY year, plot_id;
```

####Exercise 7
Write a query that returns the genus, the species, and the weight of every individual captured at the site

```
SELECT species.genus, species.species, surveys.weight
FROM surveys
JOIN species ON surveys.species_id = species.species_ID;
```

####Exercise 8
Write a query that returns the genus, species, plot type and average weights (rounded to two decimal places) for each species of individual captured, reported by species_ID and plot type and ordered from the lowest weight to the highest. Exclude all records that don't have weight values recorded.

```
SELECT species.genus, species.species, plots.plot_type, ROUND(AVG(surveys.weight), 2)
FROM surveys
JOIN species ON surveys.species_id = species.species_ID
JOIN plots ON plots.plot_ID = surveys.plot_id
WHERE surveys.weight > 0
GROUP BY plots.plot_type, species.species_id
ORDER BY AVG(surveys.weight);
```

####Exercise 9
Using a SET OPERATOR (EXCEPT) all the species found in 2000 but not in 2002, report back species

```
SELECT DISTINCT species.genus, species.species, species.species_ID
FROM surveys
JOIN species ON surveys.species_id = species.species_ID
WHERE surveys.year=1977
EXCEPT
SELECT DISTINCT species.genus, species.species, species.species_ID
FROM surveys
JOIN species ON surveys.species_id = species.species_ID
WHERE surveys.year = 2002;
```
