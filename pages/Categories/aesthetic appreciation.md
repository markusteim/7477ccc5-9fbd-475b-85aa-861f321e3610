---
title: Aesthetic appreciation, 2022-2023
---

# Summary
### Overall, the aesthetic appreciation of the hotel's material aspects leans heavily towards the positive, with roughly 91% of guests expressing admiration for the hotel's design, views, architecture, and beauty. Positive reviews: 263; Negative reviews: 24; Neutral reviews: 2.

 

#### Positive:

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

#### Negative:

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

#### Most Positive Examples:

1. "balcony's panoramic view was simply stunning"
2. "room has a spectacular view on Dubai Marina and Palm Jumeirah"
3. "property is very new, which means everything is in excellent condition"
4. "rooms are of spectacular standards"
5. "amazing hotel with striking view"

 

#### Most Negative Examples:

1. "view from this place was just so-so"
2. "huge building site and motorways"
3. "current view from my room and the pool was a building site"
4. "pool looks out into what looks like a war zone"
5. "you cannot see the palm all the what you see is just a street"


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
SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    COUNT(CAST(value AS INTEGER)) AS Polarity_sum,
    polarity
FROM
    hotels.titles
WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
AND TRIM(Category) = 'aesthetic appreciation'
GROUP BY
    TRIM(LOWER(Category)),
    polarity
ORDER BY
    Polarity_sum DESC
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
        '#3D9970',  // A shade of dark green
        '#2ECC40',      // A shade of bright green
        '#AAAAAA',       // A shade of grey
        '#FF4136',      // A shade of red
        '#85144B'  // A shade of dark red
        ]
    }
/>





# Snippets from reviews

## Positive Snippets
```sql positive_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Headline ASC
```

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

## Neutral Snippets

```sql neutral_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Headline ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>

## Negative Snippets

```sql negative_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'negative' OR polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Headline ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>


## Customer sentiment distribution (2022-2023)

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