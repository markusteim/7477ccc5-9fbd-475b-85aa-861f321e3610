# Summary

Overall, the sentiment regarding lighting and visibility in the hotel is split between positive and negative, with 40% of the feedback being positive and 40% being negative. Positive reviews: 2; Negative reviews: 2; Neutral reviews: 1.


## Positive:

1. Clean Ambiance: Guests appreciated the cleanliness that left the hotel shining, enhancing the bright
atmosphere.
2. Ample Lighting: The rooms were well-lit, providing guests with everything needed for a comfortable
stay, contributing to the overall spacious feel.

## Negative:

1. Curtain Issues: One guest reported that the black-out curtains were not entirely effective, with corners
uncovered, allowing light to seep in during the morning.
2. Room Brightness: There was a suggestion to improve and increase the brightness in the rooms,
indicating some dissatisfaction with the current lighting situation.

<br>

## Most Positive Examples:

1. "left it shining"
2. "light with everything what we need for comfortable staying"
3. "crystal clear"

 

## Most Negative Examples:

1. "black-out curtains in my room arn't 100 percent effective"
2. "significant light still coming in the morning"
3. "it will be better if you improve and increase brightness of rooms"

<br>

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
    AND TRIM(Category) = 'visual'
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
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>


<br>

# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'visual'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'visual'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<Tabs>
    <Tab label="Positive Headlines">
        <DataTable data="{positive_headlines}" search="true" rows=18 rowShading=true/>
    </Tab>
    <Tab label="Positive Snippets">
        <DataTable data="{positive_snippets}" search="true" rows=18 rowShading=true/>
    </Tab>
</Tabs>

<br>

```sql neutral_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'visual'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'visual'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<Tabs>
    <Tab label="Neutral Headlines">
        <DataTable data="{neutral_headlines}" search="true" rows=40 rowShading=true/>
    </Tab>
    <Tab label="Neutral Snippets">
        <DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>
    </Tab>
</Tabs>

<br>

```sql negative_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'visual'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'visual'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<Tabs>
    <Tab label="Negative Headlines">
        <DataTable data="{negative_headlines}" search="true" rows=40 rowShading=true/>
    </Tab>
    <Tab label="Negative Snippets">
        <DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>
    </Tab>
</Tabs>

<br>

# Customer sentiment distribution (2022-2023)

```sql sentiment_distribution
WITH Polarity_Ordered AS (
  SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    Year, -- Extract the year from the travel_date
    COUNT(DISTINCT review_id) AS ReviewCount, -- Count unique review IDs
    CASE
      WHEN TRIM(LOWER(polarity)) = 'very negative' THEN 1
      WHEN TRIM(LOWER(polarity)) = 'negative' THEN 2
      WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 3
      WHEN TRIM(LOWER(polarity)) = 'positive' THEN 4
      WHEN TRIM(LOWER(polarity)) = 'very positive' THEN 5
      ELSE 6 -- For any other case, if needed
    END AS OrderIndex
  FROM
    hotels.titles
  WHERE
    travel_date BETWEEN '2022-01-01' AND '2023-12-31'
    AND TRIM(LOWER(Category)) = 'visual' -- Change category as needed
  GROUP BY
    TRIM(LOWER(polarity)), 
    Year
)

SELECT
  Polarity,
  Year,
  ReviewCount
FROM Polarity_Ordered
ORDER BY
  Year,
  OrderIndex
```

<BarChart 
    data={sentiment_distribution} 
    x="Polarity" 
    y="ReviewCount"
    series="Year" 
    groupBy="Year" 
    type="grouped"
    sort=false
/>