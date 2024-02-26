---
title: Visual, 2022-2023
---

# Summary
### Overall, the sentiment regarding lighting and visibility in the hotel is split between positive and negative, with 40% of the feedback being positive and 40% being negative. Positive reviews: 2; Negative reviews: 2; Neutral reviews: 1.

 

#### Positive:

1. Clean Ambiance: Guests appreciated the cleanliness that left the hotel shining, enhancing the bright
atmosphere.
2. Ample Lighting: The rooms were well-lit, providing guests with everything needed for a comfortable
stay, contributing to the overall spacious feel.

#### Negative:

1. Curtain Issues: One guest reported that the black-out curtains were not entirely effective, with corners
uncovered, allowing light to seep in during the morning.
2. Room Brightness: There was a suggestion to improve and increase the brightness in the rooms,
indicating some dissatisfaction with the current lighting situation.

#### Most Positive Examples:

1. "left it shining"
2. "light with everything what we need for comfortable staying"
3. "crystal clear"

 

#### Most Negative Examples:

1. "black-out curtains in my room arn't 100 percent effective"
2. "significant light still coming in the morning"
3. "it will be better if you improve and increase brightness of rooms"

 

#### Most Negative Examples:

1.  "relatively isolated location"


```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'visual'
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
AND TRIM(Category) = 'visual'
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
WHERE TRIM(LOWER(Category)) = 'visual'
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
WHERE TRIM(LOWER(Category)) = 'visual'
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
WHERE TRIM(LOWER(Category)) = 'visual'
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
  AND TRIM(LOWER(Category)) = 'visual'
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