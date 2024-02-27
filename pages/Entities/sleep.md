<script>
  import noindex from '/Users/markusteimann/Desktop/report 2/components/noindex.svelte';
</script>

**Summary**

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

<br>

**Headlines and corresponding snippets from reviews**

**Positive Headlines**
```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE polarity = 'positive' OR polarity = 'very positive'
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND (
    Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
)
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{positive_headlines}" search="true" rows=40 rowShading=true/>

**Positive Snippets**
```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE polarity = 'positive' OR polarity = 'very positive'
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND (
    Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
)
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
ORDER BY Snippet ASC
```

<br>

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

**Neutral Headlines**
```sql neutral_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE polarity = 'neutral'
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND (
    Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
)
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{neutral_headlines}" search="true" rows=40 rowShading=true/>

**Neutral Snippets**
```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE polarity = 'neutral'
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND (
    Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
)
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
ORDER BY Snippet ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>

<br>

**Negative Headlines**
```sql negative_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE polarity = 'negative' or polarity = 'very negative'
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND (
    Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
)
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{negative_headlines}" search="true" rows=40 rowShading=true/>

**Negative Snippets**
```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE polarity = 'negative' or polarity = 'very negative'
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
AND (
    Snippet LIKE '%bed%'
    OR Snippet LIKE '%sleep%'
    OR Snippet LIKE '%pillow%'
    OR Snippet LIKE '%blanket%'
    OR Snippet LIKE '%mattress%'
    OR Snippet LIKE '%sheets%'
)
AND Snippet NOT LIKE '%bedroom%'
AND Snippet NOT LIKE '%bed apartment%'
ORDER BY Snippet ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>

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