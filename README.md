# Netflix Movies and TV Shows Data Analysis using SQL

## Overview

This project involves a comprehensive analysis of Netflix's Movies and TV Shows dataset using SQL.  
The goal is to extract valuable insights and answer different business questions based on the dataset.

This project demonstrates SQL skills such as:
- Data exploration
- Aggregations
- Window functions
- Data transformation
- Text analysis

---

## Objectives

- Analyze the distribution of content types (Movies vs TV Shows)
- Identify the most common ratings for Movies and TV Shows
- Analyze content based on release years, countries, and duration
- Explore and categorize content based on specific criteria and keywords

---

## Dataset

The dataset used in this project is from Kaggle.

Dataset Link:  
https://www.kaggle.com/datasets/shivamb/netflix-shows

---

## Database Schema

```sql
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

---

# Business Problems and Solutions

## 1. Count the Number of Movies vs TV Shows

```sql
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
```

**Objective:** Determine the distribution of content types on Netflix.

---

## 2. Find the Most Common Rating for Movies and TV Shows

```sql
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
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

**Objective:** Identify the most frequently occurring rating for each type of content.

---

## 3. List All Movies Released in 2020

```sql
SELECT *
FROM netflix
WHERE release_year = 2020;
```

**Objective:** Retrieve all movies released in a specific year.

---

## 4. Top 5 Countries with the Most Content

```sql
SELECT *
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```

**Objective:** Identify the countries producing the most Netflix content.

---

## 5. Identify the Longest Movie

```sql
SELECT *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration,' ',1)::INT DESC;
```

**Objective:** Find the movie with the longest duration.

---

## 6. Content Added in the Last 5 Years

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added,'Month DD, YYYY') 
>= CURRENT_DATE - INTERVAL '5 years';
```

**Objective:** Retrieve recently added Netflix content.

---

## 7. Movies/TV Shows Directed by Rajiv Chilaka

```sql
SELECT *
FROM (
    SELECT *,
    UNNEST(STRING_TO_ARRAY(director,',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
```

**Objective:** Find content directed by Rajiv Chilaka.

---

## 8. TV Shows with More Than 5 Seasons

```sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
AND SPLIT_PART(duration,' ',1)::INT > 5;
```

**Objective:** Identify long-running TV shows.

---

## 9. Count Content by Genre

```sql
SELECT
UNNEST(STRING_TO_ARRAY(listed_in,',')) AS genre,
COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```

**Objective:** Analyze content distribution across genres.

---

## 10. Top 5 Years with Highest Netflix Content Release in India

```sql
SELECT
country,
release_year,
COUNT(show_id) AS total_release,
ROUND(
COUNT(show_id)::numeric /
(SELECT COUNT(show_id)
FROM netflix
WHERE country='India')::numeric * 100,2
) AS avg_release

FROM netflix
WHERE country='India'

GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```

**Objective:** Identify peak years for Netflix content release in India.

---

## 11. Movies that are Documentaries

```sql
SELECT *
FROM netflix
WHERE listed_in LIKE '%Documentaries';
```

**Objective:** Retrieve all documentary movies.

---

## 12. Content Without a Director

```sql
SELECT *
FROM netflix
WHERE director IS NULL;
```

**Objective:** Identify content missing director information.

---

## 13. Movies Featuring Salman Khan in the Last 10 Years

```sql
SELECT *
FROM netflix
WHERE casts LIKE '%Salman Khan%'
AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

**Objective:** Count Salman Khan appearances in recent years.

---

## 14. Top 10 Actors Appearing in Indian Netflix Content

```sql
SELECT
UNNEST(STRING_TO_ARRAY(casts,',')) AS actor,
COUNT(*)
FROM netflix
WHERE country='India'

GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
```

**Objective:** Identify the most frequent actors in Indian Netflix content.

---

## 15. Categorize Content Based on Violence Keywords

```sql
SELECT
category,
COUNT(*) AS content_count

FROM
(
SELECT
CASE
WHEN description ILIKE '%kill%'
OR description ILIKE '%violence%'
THEN 'Bad'
ELSE 'Good'
END AS category

FROM netflix
) AS categorized_content

GROUP BY category;
```

**Objective:** Classify content based on sensitive keywords.

---

# Findings and Conclusion

- Netflix contains a diverse collection of movies and TV shows.
- Certain ratings dominate Netflix content, revealing target audiences.
- Content production varies significantly by country.
- India contributes a large amount of Netflix content.
- Keyword analysis helps identify violent or sensitive content.

This project demonstrates how SQL can be used to perform **real-world data analysis and answer business questions.**

---

# Author

**Uzair Khan**

This project is part of my Data Analyst portfolio showcasing SQL data analysis skills.

