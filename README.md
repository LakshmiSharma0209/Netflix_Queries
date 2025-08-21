Netflix Movies and TV Shows Data Analysis using SQL
Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. Building upon a pre-defined set of business problems and a dataset, I have implemented SQL queries to extract valuable insights and answer various business questions. This README provides a detailed account of the project's objectives, the business problems addressed, my SQL solutions, and key findings.

Objectives
Analyze the distribution of content types (movies vs TV shows).

Identify the most common ratings for movies and TV shows.

List and analyze content based on release years, countries, and durations.

Explore and categorize content based on specific criteria and keywords.

Showcase my SQL querying, data manipulation, and analytical skills.

Dataset
The data for this project is sourced from the Kaggle dataset:

Dataset Link: Netflix Movies and TV Shows

Schema
```
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
show_id VARCHAR(5),
type VARCHAR(10),
title VARCHAR(250),
director VARCHAR(550),
casts VARCHAR(1050),
country VARCHAR(550),
date_added VARCHAR(55),
release_year INT,
rating VARCHAR(15),
duration VARCHAR(15),
listed_in VARCHAR(250),
description VARCHAR(550)
);
```
Business Problems and My Solutions
Here, I've provided my SQL solutions to the specified business problems. The queries demonstrate various SQL concepts, including joins, subqueries, CTEs, window functions, and string manipulation.

1. Count the Number of Movies vs TV Shows
  ```
   SELECT
   type,
   COUNT(\*)
   FROM netflix
   GROUP BY 1;
```
Objective: Determine the distribution of content types on Netflix.

2. Find the Most Common Rating for Movies and TV Shows
```
   WITH RatingCounts AS (
   SELECT
   type,
   rating,
   COUNT(\*) AS rating_count
   FROM netflix
   GROUP BY type, rating
   ),
   RankedRatings AS (
   SELECT
   type,
   rating,
   rating_count,
   RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
   FROM RatingCounts
   )
   SELECT
   type,
   rating AS most_frequent_rating
   FROM RankedRatings
   WHERE rank = 1;
```
Objective: Identify the most frequently occurring rating for each type of content.

3. List All Movies Released in a Specific Year (e.g., 2020)

```
   SELECT \* FROM netflix
   WHERE release_year = 2020;
```
Objective: Retrieve all movies released in a specific year.

5. Find the Top 5 Countries with the Most Content on Netflix
```
    SELECT _FROM
   (
   SELECT
   UNNEST(STRING_TO_ARRAY(country, ',')) as country,
   COUNT(_) as total_content
   FROM netflix
   GROUP BY 1
   )as t1
   WHERE country IS NOT NULL
   ORDER BY total_content DESC
   LIMIT 5;
```
Objective: Identify the top 5 countries with the highest number of content items.

5. Identify the Longest Movie
```
   SELECT \*
   FROM netflix
   WHERE type = 'Movie'
   ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
```
Objective: Find the movie with the longest duration.

6. Find Content Added in the Last 5 Years
```
   SELECT

- FROM netflix
  WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```
Objective: Retrieve content added to Netflix in the last 5 years.

7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'
```
   SELECT _
   FROM (
   SELECT
   _,
   UNNEST(STRING_TO_ARRAY(director, ',')) as director_name
   FROM
   netflix
   ) AS t
   WHERE
   director_name = 'Rajiv Chilaka';
```
Objective: List all content directed by 'Rajiv Chilaka'.

8. List All TV Shows with More Than 5 Seasons
```
   SELECT \*
   FROM netflix
   WHERE
   TYPE = 'TV Show'
   AND
   SPLIT_PART(duration, ' ', 1)::INT > 5;
```
Objective: Identify TV shows with more than 5 seasons.

9. Count the Number of Content Items in Each Genre
```
   SELECT
   UNNEST(STRING_TO_ARRAY(listed_in, ',')) as genre,
   COUNT(\*) as total_content
   FROM netflix
   GROUP BY 1;
```
Objective: Count the number of content items in each genre.

10. Find each year and the average numbers of content release in India on Netflix. Return top 5 years with highest average content release!
```
     SELECT
    country,
    release_year,
    COUNT(show_id) as total_release,
    ROUND(
    COUNT(show_id)::numeric/
    (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric \* 100
    ,2
    )
    as avg_release
    FROM netflix
    WHERE country = 'India'
    GROUP BY country, 2
    ORDER BY avg_release DESC
    LIMIT 5;
```
Objective: Calculate and rank years by the average percentage of content releases by India relative to India's total releases.

11. List All Movies that are Documentaries
```
     SELECT \* FROM netflix
    WHERE listed_in LIKE '%Documentaries';
```
Objective: Retrieve all movies classified as documentaries.

12. Find All Content Without a Director
```
     SELECT \* FROM netflix
    WHERE director IS NULL;
```
Objective: List content that does not have a director.

13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
```
     SELECT \* FROM netflix
    WHERE
    casts LIKE '%Salman Khan%'
    AND
    release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```
Objective: Count the number of movies featuring 'Salman Khan' released in the last 10 years.

14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India
```
     SELECT
    UNNEST(STRING_TO_ARRAY(casts, ',')) as actor,
    COUNT(\*)
    FROM netflix
    WHERE country = 'India'
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 10;
```
Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.

15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords
```
     SELECT
    category,
    TYPE,
    COUNT(_) AS content_count
    FROM (
    SELECT
    _,
    CASE
    WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
    ELSE 'Good'
    END AS category
    FROM netflix
    ) AS categorized_content
    GROUP BY 1,2
    ORDER BY 2;
```
Objective: Categorize content as 'Bad' if its description contains 'kill' or 'violence' keywords and 'Good' otherwise. Count the number of items in each category.

My Learnings and Key Findings
Through this project, I gained practical experience with:

Advanced SQL Techniques: Utilizing CTEs (WITH clause), window functions (RANK() OVER(), UNNEST, STRING_TO_ARRAY), and string manipulation functions (SPLIT_PART, ILIKE).

Data Cleaning and Transformation: Handling messy data (e.g., comma-separated strings in country and casts) to enable effective analysis.

Date and Time Functions: Working with TO_DATE, CURRENT_DATE, INTERVAL, and EXTRACT to filter data based on timeframes.

Problem Solving: Breaking down complex business questions into manageable SQL queries.

Understanding Data Nuances: Recognizing how data is stored (e.g., duration as string) and applying appropriate transformations.

This analysis provides a comprehensive view of Netflix's content and demonstrates a strong foundation in SQL for data analysis.
