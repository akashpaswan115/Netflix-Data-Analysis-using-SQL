# Netflix Movies and TV Shows Data Analysis using SQL
![image](https://github.com/user-attachments/assets/40508b3f-5a21-4ea6-b3d4-5bc30472a2d3)
Sure! Here's a more **interactive and cool** version of your README file. The core content remains unchanged — only the **vocabulary, tone, and structure** have been enhanced to make it more engaging and professional:

---

# 🎬 Netflix Movies & TV Shows Analysis with SQL

Welcome to a deep dive into Netflix's treasure trove of movies and TV shows — all powered by SQL! 📊 This project aims to uncover actionable insights and answer critical business questions using real-world data.

---

## 🎯 Objectives

* Analyze how content is distributed between Movies and TV Shows.
* Discover the most common ratings across both categories.
* Examine content trends based on release years, countries, and durations.
* Classify and explore content using keywords and customized filters.

---

## 📦 Dataset

Sourced directly from Kaggle:

🔗 **[Movies Dataset on Kaggle](https://www.kaggle.com/datasets/shivamb/netflix-shows)**

---

## 🗂️ Database Schema

```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

---

## 💼 Business Questions & SQL Solutions

### 1️⃣ Distribution of Movies vs TV Shows

```sql
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
```

📌 *Goal: Understand the content type breakdown on Netflix.*

---

### 2️⃣ Most Frequent Ratings for Each Type

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

📌 *Goal: Identify the most popular ratings by content type.*

---

### 3️⃣ Movies Released in a Specific Year (e.g., 2020)

```sql
SELECT * 
FROM netflix
WHERE release_year = 2020;
```

📌 *Goal: Fetch all titles released in a given year.*

---

### 4️⃣ Top 5 Countries by Content Volume

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

📌 *Goal: Find countries with the most Netflix content.*

---

### 5️⃣ Longest Movie on Netflix

```sql
SELECT 
    *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
```

📌 *Goal: Identify the longest-duration movie.*

---

### 6️⃣ Recently Added Content (Last 5 Years)

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

📌 *Goal: Extract content added in the past five years.*

---

### 7️⃣ Works Directed by 'Rajiv Chilaka'

```sql
SELECT *
FROM (
    SELECT 
        * ,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
```

📌 *Goal: List all content directed by a specific director.*

---

### 8️⃣ TV Shows with More Than 5 Seasons

```sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
```

📌 *Goal: Find long-running TV shows.*

---

### 9️⃣ Content Count per Genre

```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```

📌 *Goal: Analyze content distribution by genre.*

---

### 🔟 Top Years for Content Released in India (Avg %)

```sql
SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric / 
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```

📌 *Goal: Identify years with the highest average content released in India.*

---

### 1️⃣1️⃣ All Movies Labeled as Documentaries

```sql
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
```

📌 *Goal: Retrieve all documentaries.*

---

### 1️⃣2️⃣ Content Without a Listed Director

```sql
SELECT * 
FROM netflix
WHERE director IS NULL;
```

📌 *Goal: Find content missing director information.*

---

### 1️⃣3️⃣ Salman Khan Appearances in the Last 10 Years

```sql
SELECT * 
FROM netflix
WHERE casts LIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

📌 *Goal: Count Salman Khan’s recent appearances.*

---

### 1️⃣4️⃣ Top 10 Most Featured Actors in Indian Movies

```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
```

📌 *Goal: List actors with the most appearances in Indian content.*

---

### 1️⃣5️⃣ Keyword-Based Content Categorization (Kill/Violence)

```sql
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
```

📌 *Goal: Tag content as 'Bad' if certain violent keywords are present.*

---

## 📌 Key Insights & Conclusion

* **Content Variety**: Netflix hosts a balanced mix of TV shows and movies with diverse themes and genres.
* **Audience Targeting**: Rating distribution highlights audience preferences and content strategies.
* **Regional Trends**: Top-performing countries and India's yearly output shed light on geographical content trends.
* **Content Labeling**: Keyword classification reveals the tone and themes of available titles.
---
