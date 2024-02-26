---
title: Sleep, 2022-2023
---

```sql sum_by_category
SELECT
    TRIM(LOWER(Category)) AS category_name,
    SUM(CAST(value AS INTEGER)) AS category_value_sum,
    polarity
FROM
    hotels.titles
WHERE travel_date >= '2022-01-01' 
    AND travel_date <= '2023-12-31' 
    AND Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
    AND Snippet NOT LIKE '%bedroom%'
    AND Snippet NOT LIKE '%bed apartment%'
GROUP BY
    TRIM(LOWER(Category)),
    polarity
ORDER BY
    category_value_sum DESC
```

<BarChart 
    data={sum_by_category} 
    swapXY=true 
    x=category_name 
    y=category_value_sum 
    series=polarity
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
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND Snippet LIKE '%bed%'
OR Snippet LIKE '%sleep%'
OR Snippet LIKE '%pillow%'
OR Snippet LIKE '%blanket%'
OR Snippet LIKE '%mattress%'
OR Snippet LIKE '%sheets%'
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
ORDER BY Headline ASC
```

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

## Neutral Snippets

```sql neutral_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND Snippet LIKE '%bed%'
OR Snippet LIKE '%sleep%'
OR Snippet LIKE '%pillow%'
OR Snippet LIKE '%blanket%'
OR Snippet LIKE '%mattress%'
OR Snippet LIKE '%sheets%'
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
ORDER BY Headline ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>

## Negative Snippets

```sql negative_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'negative' OR polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND Snippet LIKE '%bed%'
OR Snippet LIKE '%sleep%'
OR Snippet LIKE '%pillow%'
OR Snippet LIKE '%blanket%'
OR Snippet LIKE '%mattress%'
OR Snippet LIKE '%sheets%'
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
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
  AND Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
    AND Snippet NOT LIKE '%bedroom%'
    AND Snippet NOT LIKE '%bed apartment%'
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