
# Overview 2022-2023

 ```sql titles
 select * from hotels.titles 
 WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
 ```

1. Aesthetic appreciation (<Value data={category_propositions} column=percentage row=0/>%)
2. Comfort & clean (<Value data={category_propositions} column=percentage row=1/>%)
3. Fun & stress-free (<Value data={category_propositions} column=percentage row=2/>%)
4. Impression (<Value data={category_propositions} column=percentage row=3/>%)
5. Safety (<Value data={category_propositions} column=percentage row=4/>%)
6. Smell (<Value data={category_propositions} column=percentage row=5/>%)
7. Sound (<Value data={category_propositions} column=percentage row=6/>%)
8. Taste (<Value data={category_propositions} column=percentage row=7/>%)
9. Value & values (<Value data={category_propositions} column=percentage row=8/>%)
10. Visual (<Value data={category_propositions} column=percentage row=9/>%)
11. Welcome & valued (<Value data={category_propositions} column=percentage row=10/>%)
12. Well-being (<Value data={category_propositions} column=percentage row=11/>%)


```sql category_propositions
WITH CategoryCounts AS (
    SELECT
        TRIM(LOWER(Category)) AS Category,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(LOWER(Category)) IN ('welcome & valued', 'fun & stress-free','impression', 'comfort & clean', 'taste', 'aesthetic appreciation', 'well-being', 'value & values', 'sound', 'safety', 'smell', 'visual', 'fun & stress-free')
    GROUP BY
        TRIM(LOWER(Category))
),
TotalReviewCount AS (
    SELECT
        SUM(category_count) AS total
    FROM
        CategoryCounts
)
SELECT
    cc.Category,
    cc.category_count,
    CASE WHEN trc.total > 0 THEN 
        ROUND((cc.category_count * 100.0) / trc.total, 2) 
    ELSE 
        0 
    END AS percentage
FROM
    CategoryCounts cc
CROSS JOIN TotalReviewCount trc
ORDER BY
    cc.Category

```


```sql sum_by_category
SELECT
    TRIM(LOWER(Category)) AS category_name,
    SUM(CAST(value AS INTEGER)) AS category_value_sum,
    polarity
FROM
    hotels.titles
WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
GROUP BY
    TRIM(LOWER(Category)),
    polarity
ORDER BY
    CASE polarity
        WHEN 'very negative' THEN 1
        WHEN 'negative' THEN 2
        WHEN 'neutral' THEN 3
        WHEN 'positive' THEN 4
        WHEN 'very positive' THEN 5
    END,
    category_name ASC
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
        "#85144B", // A shade of dark red
        "#FF4136", // A shade of red
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>

## Customer sentiment distribution (2022-2023)
```sql sum_by_polarity
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN review_id ELSE NULL END) AS Negative,
  COUNT(DISTINCT CASE WHEN polarity = 'neutral' THEN review_id ELSE NULL END) AS Neutral,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN review_id ELSE NULL END) AS Positive
FROM
  titles
WHERE
  travel_date BETWEEN '2022-01-01' AND '2023-12-31'
GROUP BY
  Category
ORDER BY 
  Category ASC

```

<DataTable data={sum_by_polarity} rows={12}>
    <Column id="Category" title="Category" />
    <Column id="Negative" title="Negative" contentType=colorscale scaleColor=red/>
    <Column id="Neutral" title="Neutral" contentType=colorscale scaleColor=grey/>
    <Column id="Positive" title="Positive" contentType=colorscale scaleColor=green/>
</DataTable>

## Number of customers with NEGATIVE experiences (2022-2023)
```sql negative_reviews_2022
-- For the year 2022
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN review_id ELSE NULL END) AS negative_count,
  polarity
FROM
  titles
WHERE
  travel_date BETWEEN '2022-01-01' AND '2022-12-31'AND
  polarity in ('negative', 'very negative')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

```sql negative_reviews_2023
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN review_id ELSE NULL END) AS negative_count,
  polarity
FROM
  titles
WHERE
  travel_date BETWEEN '2023-01-01' AND '2023-12-31'AND
  polarity in ('negative', 'very negative')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

<div style="display: flex; justify-content: space-between;">
  <div style="width: 50%;">
    <!-- Table for 2022 -->
    <BarChart 
        data={negative_reviews_2022} 
        swapXY=true 
        x=Category
        y=negative_count 
        series=polarity
        sort=false
        title=2022
        colorPalette={
        [
        '#FF4136',      // A shade of red
        '#85144B'  // A shade of dark red
        ]
    }
    />
  </div>
  <div style="width: 50%;">
    <!-- Table for 2023 -->
    <BarChart 
        data={negative_reviews_2023} 
        swapXY=true 
        x=Category
        y=negative_count 
        series=polarity
        sort=false
        title=2023
        colorPalette={
        [
        '#FF4136',      // A shade of red
        '#85144B'  // A shade of dark red
        ]
    }
        
    />
  </div>
</div>

## Number of customers with POSITIVE experiences (2022-2023)
```sql positive_reviews_2022
-- For the year 2022
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN review_id ELSE NULL END) AS positive_count,
  polarity
FROM
  titles
WHERE
  travel_date BETWEEN '2022-01-01' AND '2022-12-31'AND
  polarity in ('positive', 'very positive')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

```sql positive_reviews_2023
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN review_id ELSE NULL END) AS positive_count,
  polarity
FROM
  titles
WHERE
  travel_date BETWEEN '2023-01-01' AND '2023-12-31'AND
  polarity in ('positive', 'very positive')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

<div style="display: flex; justify-content: space-between;">
  <div style="width: 50%;">
    <!-- Table for 2022 -->
    <BarChart 
        data={positive_reviews_2022} 
        swapXY=true 
        x=Category
        y=positive_count 
        series=polarity
        sort=false
        title=2022
        colorPalette={
        [
        '#3D9970',  // A shade of dark green
        '#2ECC40',      // A shade of bright green
        ]
    }
    />
  </div>
  <div style="width: 50%;">
    <!-- Table for 2023 -->
    <BarChart 
        data={positive_reviews_2023} 
        swapXY=true 
        x=Category
        y=positive_count 
        series=polarity
        sort=false
        title=2023
        colorPalette={
        [
        '#3D9970',  // A shade of dark green
        '#2ECC40',      // A shade of bright green
        ]
    }
    />
  </div>
</div>

