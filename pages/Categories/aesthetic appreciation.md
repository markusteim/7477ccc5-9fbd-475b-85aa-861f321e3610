

**Summary**

Overall, the aesthetic appreciation of the hotel's material aspects leans heavily towards the positive, with roughly 91% of guests expressing admiration for the hotel's design, views, architecture, and beauty. 
Positive reviews: 263; Negative reviews: 24; Neutral reviews: 2.

 

**Positive:**

1. Stunning Views: Guests were captivated by the breathtaking vistas, from the panoramic balcony views
to the spectacular sights of the sea, Dubai Marina, and iconic landmarks like Burj Al Arab.
2. Modern Design: The hotel's contemporary and stylish design received high praise, with its new
facilities, fresh furniture, and modern decor creating an inviting atmosphere.
3. Luxurious Ambiance: The ambiance and decor of the hotel were described as luxurious and tastefully
done, contributing to a premium feel and a comfortable stay.
4. Exceptional Facilities: The hotel's amenities, including the infinity pool and well-equipped rooms, were
highlighted as exceptional, enhancing the overall guest experience.
5. Strategic Location: The hotel's location was lauded for its convenience and strategic proximity to
attractions, with many rooms offering stunning views of the surrounding scenery.

**Negative:**

1. Construction Issues: Some guests were disappointed by the presence of construction sites, which
obstructed views and impacted the hotel's tranquility.
2. Limited Views: A few reviewers expressed discontent with their room views, expecting more
picturesque scenes rather than construction or street views.
3. Outdated Elements: Despite the hotel's modernity, there were mentions of certain areas, like the
bathrooms, needing updates or renovations.
4. Misleading Descriptions: Guests found some room descriptions misleading, particularly regarding the
promised views of landmarks or the sea.
5. Comparative Disadvantages: A sense of the hotel not living up to the standards of neighboring
properties was noted, with some guests feeling it lacked the same level of luxury or view quality.

<br>

**Most Positive Examples:**

1. "balcony's panoramic view was simply stunning"
2. "room has a spectacular view on Dubai Marina and Palm Jumeirah"
3. "property is very new, which means everything is in excellent condition"
4. "rooms are of spectacular standards"
5. "amazing hotel with striking view"

 

**Most Negative Examples:**

1. "view from this place was just so-so"
2. "huge building site and motorways"
3. "current view from my room and the pool was a building site"
4. "pool looks out into what looks like a war zone"
5. "you cannot see the palm all the what you see is just a street"

<br>

```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'aesthetic appreciation'
    GROUP BY
        TRIM(polarity)
),
TotalReviews AS (
    SELECT
        SUM(category_count) AS total
    FROM
        CategoryCounts
)
SELECT
    a.CleanCategory AS Category,
    a.category_count,
    ROUND((a.category_count * 100.0) / b.total, 2) AS percentage
FROM
    CategoryCounts a, TotalReviews b
ORDER BY percentage DESC
```

```sql sum_by_polarity
WITH Polarity_Ordered AS (
  SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    COUNT(CAST(value AS INTEGER)) AS Polarity_sum,
    CASE
      WHEN TRIM(LOWER(polarity)) = 'very negative' THEN 1
      WHEN TRIM(LOWER(polarity)) = 'negative' THEN 2
      WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 3
      WHEN TRIM(LOWER(polarity)) = 'positive' THEN 4
      WHEN TRIM(LOWER(polarity)) = 'very positive' THEN 5
      ELSE 6
    END AS OrderIndex
  FROM
    hotels.titles
  WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'aesthetic appreciation'
  GROUP BY
    TRIM(LOWER(polarity))
)

SELECT
  Polarity,
  Polarity_sum
FROM Polarity_Ordered
ORDER BY OrderIndex

```

<BarChart 
    data={sum_by_polarity} 
    swapXY=false
    x=Polarity
    y=Polarity_sum 
    series=Polarity
    sort=false
    colorPalette={
        [
        "#FF4136", // A shade of red
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>



<br>

**Headlines and corresponding snippets from reviews**

<br>

**Positive Headlines**

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{positive_headlines}" search="true" rows=40 rowShading=true/>

**Positive Snippets**
```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

**Neutral Headlines**
```sql neutral_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{neutral_headlines}" search="true" rows=40 rowShading=true/>

**Neutral Snippets**
```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>


**Negative Headlines**
```sql negative_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{negative_headlines}" search="true" rows=40 rowShading=true/>


**Negative Snippets**
```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>


<br>

**Customer sentiment distribution (2022-2023)**

```sql sentiment_distribution
SELECT
  TRIM(LOWER(polarity)) AS Polarity,
  Year,
  COUNT(DISTINCT review_id) AS ReviewCount
FROM
  hotels.titles
WHERE
  travel_date BETWEEN '2022-01-01' AND '2023-12-31'
  AND TRIM(LOWER(Category)) = 'aesthetic appreciation'
GROUP BY
  Polarity,
  Year

```

<BarChart 
    data={sentiment_distribution} 
    x="Polarity" 
    y="ReviewCount"
    series="Year" 
    groupBy="Year" 
    type="grouped"
    
/>