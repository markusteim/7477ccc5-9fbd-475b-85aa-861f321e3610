
**Overview 2022-2023**



 ```sql titles
 select * from hotels.titles 
 WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
 ```

**Summary**

Across the 12 categories, hotel guests generally shared more positive experiences than negative ones. The overall sentiment was heavily skewed towards satisfaction, with an estimated 76.8% positive feedback compared to 8.1% negative and 15.2% neutral. Out of the total comments, 1,797 were positive, 189 were negative and 355 were neutral. The distribution of comments across categories was as follows: 


1. Welcome & Valued (23.73%), 
2. Impression (21.17%)
3. Comfort & clean (14.81%)
4. Taste (12.65%) 
5. Fun & stress-free (9.54%)
6. Aesthetic appreciation (8.89%)
7. Well-being (3.69%)
8. Value & values (2.56%)
9. Sound (1.09)
10. Safety (0.93%)
11. Smell (0.23%)
12. Visual (0.21%)


```sql category_propositions
WITH CategoryCounts AS (
    SELECT
        TRIM(Category) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    GROUP BY
        TRIM(Category)
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

**Customer sentiment distribution (2022-2023)**
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

```

<DataTable data={sum_by_polarity} rows={12}>
    <Column id="Category" title="Category" />
    <Column id="Negative" title="Negative" contentType=colorscale scaleColor=red/>
    <Column id="Neutral" title="Neutral" contentType=colorscale scaleColor=grey/>
    <Column id="Positive" title="Positive" contentType=colorscale scaleColor=green/>
</DataTable>

**Number of customers with NEGATIVE experiences (2022-2023)**
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

**Number of customers with POSITIVE experiences (2022-2023)**
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
