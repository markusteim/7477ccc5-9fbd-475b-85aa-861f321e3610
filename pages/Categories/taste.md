---
title: Taste, 2022-2023
---

# Summary
### Positive sentiment dominates the taste experiences at the hotel, with approximately 88.5% of the reviews being positive. Total positive reviews: 391; Total negative reviews: 13. Total neutral reviews : 38

 

#### Positive:

1. Breakfast Delights: Guests rave about the "best breakfast ever," with a "superb choice" and "dreamy"
inclusive options.
2. Culinary Excellence: The hotel's restaurant impresses with "five-star meals," "fantastic buffets," and
"most delicious mushroom soup."
3. Special Offers: Promotions like the "best wine deal" add value to guests' dining experiences.
4. Diverse Options: The variety at breakfast and dinner is celebrated, with "amazing spreads" and
"delicious and varied" buffets.
5. Personalized Service: Chefs accommodate specific requests, like cooking "specific types of eggs,"
enhancing the personalized dining experience.
 

#### Negative:

1. Vegetarian Concerns: Some guests note a lack of vegetarian options at dinner and a desire for more
variety.
2. Quality Issues: There are mentions of "low-quality bread," "stale pastries," and "almost raw scrambled
eggs," indicating room for improvement.
3. Dietary Restrictions: Vegan guests express disappointment with the limited options and labeling for
vegan dishes.
4. Freshness Lapses: Comments about "cold" poached eggs and "not much refreshing" juices suggest
some inconsistencies in food preparation.
5. Hygiene Preference: One guest suggests that single portions might be more hygienic than the current
buffet setup.

#### Most Positive Examples:

1. "best breakfast i have ever had"
2. "if you want a specific type of eggs, the chef will cook it for you"
3. "most delicious mushroom soup ever"
4. "breakfast was just superb"
5. "we had breakfast inclusive and its like in a dream"

 

#### Most Negative Examples:

1. "dinner sometimes lacked vegetarian options"
2. "bread was low in quality"
3. "freshly made poached eggs are cold inside"
4. "good breakfast but the pastries could be fresher than stale"
5. "i still don't know why almost every buffet in dubai has almost raw scrambled eggs"


```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'taste'
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
AND TRIM(Category) = 'taste'
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




# Headlines and corresponding snippets from reviews

## Positive Headlines
```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{positive_headlines}" search="true" rows=40 rowShading=true/>

## Positive Snippets
```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

## Neutral Headlines
```sql neutral_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{neutral_headlines}" search="true" rows=40 rowShading=true/>

## Neutral Snippets
```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>

## Negative Headlines
```sql negative_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{negative_headlines}" search="true" rows=40 rowShading=true/>

## Negative Snippets
```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
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
  AND TRIM(LOWER(Category)) = 'taste'
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