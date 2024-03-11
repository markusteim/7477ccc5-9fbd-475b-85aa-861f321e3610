# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Overview George Washington University


 ```sql titles
 select * from hotels.titles 
 WHERE date >= '2009-01-01' AND date <= '2023-12-31'
 ```


1. Aesthetics (<Value data={category_propositions} column=percentage row=0/>%)
2. Belonging & welcomed (<Value data={category_propositions} column=percentage row=1/>%)
3. Comfort & clean (<Value data={category_propositions} column=percentage row=2/>%)
4. Empowerment, success & influence (<Value data={category_propositions} column=percentage row=3/>%)
5. Fun & stress-free (<Value data={category_propositions} column=percentage row=4/>%)
6. Impressions (<Value data={category_propositions} column=percentage row=5/>%)
7. Safety (<Value data={category_propositions} column=percentage row=6/>%)
8. Self-worth, pride & self-awareness (<Value data={category_propositions} column=percentage row=7/>%)
9. Sexual (<Value data={category_propositions} column=percentage row=8/>%)
10. Smells (<Value data={category_propositions} column=percentage row=9/>%)
11. Sounds (<Value data={category_propositions} column=percentage row=10/>%)
12. Tastes (<Value data={category_propositions} column=percentage row=11/>%)
13. Value & values (<Value data={category_propositions} column=percentage row=12/>%)
14. Visibility (<Value data={category_propositions} column=percentage row=13/>%)
15. Well-being (<Value data={category_propositions} column=percentage row=14/>%)

# Summary

The overall sentiment from the university reviews presents a mixed picture, with a notable lean towards positive experiences, particularly in terms of the school's location, opportunities for internships, and specific programs like international affairs. However, there are recurring negative themes related to certain aspects of campus life, such as housing and the dining experience.

**Impressions:**
The impressions of the university are generally positive, with many students expressing satisfaction with their decision to attend and recommending the school to others. The heart of D.C. location is frequently praised, and the school's reputation seems to carry weight, leading to positive receptions from others when mentioned. However, there are voices of discontent, with some students feeling that the university could handle certain situations better and that the weight of the school's name does not always meet expectations.

**Housing:**
Housing receives mixed reviews, with some students enjoying their dorm experiences, particularly in Thurston Hall, while others find it a hassle, especially when placed on the Mount Vernon campus. The inconvenience of the Vern Express and the desire for better housing options are common complaints.

**Dining:**
Dining services are a significant point of contention. J Street, in particular, is often criticized for its lack of variety and quality. Some students express a desire for better options and the ability to use their dining dollars more flexibly.

**Greek Life:**
Greek life has a neutral to negative perception, with a minority of the student body participating. Some students feel indifferent or actively dislike the Greek scene, while others see it as a minor yet noticeable part of campus life.

**Academics:**
Academic experiences are largely positive, with specific praise for programs like international affairs and public health. Professors and internship opportunities in D.C. are highlighted as strengths. However, some students feel that certain majors are not as well supported or that the academic rigor could be improved.

**Campus Life:**
Campus life is seen as vibrant due to the urban setting, but sports and athletics do not play a significant role in the overall student experience. The university's integration into the city is appreciated, though some students feel that campus life suffers as a result.

In summary, George Washington University is often seen as a great school with valuable opportunities, particularly for those interested in politics and international affairs. However, aspects like housing, dining, and the prominence of Greek life receive mixed reviews, indicating room for improvement in the overall student experience.


```sql category_propositions
WITH AllCategories AS (
    SELECT 'aesthetics' AS Category
    UNION ALL SELECT 'belonging & welcomed'
    UNION ALL SELECT 'comfort & clean'
    UNION ALL SELECT 'empowerment, success & influence'
    UNION ALL SELECT 'fun & stress-free'
    UNION ALL SELECT 'impressions'
    UNION ALL SELECT 'safety'
    UNION ALL SELECT 'self-worth, pride & self-awareness'
    UNION ALL SELECT 'sexual'  Stdeu
    UNION ALL SELECT 'smells'
    UNION ALL SELECT 'sounds'
    UNION ALL SELECT 'tastes'
    UNION ALL SELECT 'value & values'
    UNION ALL SELECT 'visibility'
    UNION ALL SELECT 'well-being'
),
CategoryCounts AS (
    SELECT
        TRIM(LOWER(Category)) AS Category,
        COUNT(DISTINCT Snippet) AS category_count
    FROM
        hotels.titles
    GROUP BY
        TRIM(LOWER(Category))
),
TotalReviewCount AS (
    SELECT
        SUM(category_count) AS total
    FROM
        CategoryCounts
)
SELECT ac.Category,
       COALESCE(cc.category_count, 0) AS category_count,
       CASE WHEN trc.total > 0 THEN ROUND((cc.category_count * 100.0) / trc.total, 2) ELSE 0 END AS percentage
FROM AllCategories ac
LEFT JOIN CategoryCounts cc ON ac.Category = cc.Category
LEFT JOIN TotalReviewCount trc ON 1 = 1
ORDER BY ac.Category


```


```sql sum_by_category
SELECT
    TRIM(LOWER(Category)) AS category_name,
    SUM(CAST(value AS INTEGER)) AS category_value_sum,
    polarity
FROM
    hotels.titles
WHERE date >= '2009-01-01' AND date <= '2023-12-31'
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

## Student sentiment distribution (2019-2023)
```sql sum_by_polarity
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN id ELSE NULL END) AS Negative,
  COUNT(DISTINCT CASE WHEN polarity = 'neutral' THEN id ELSE NULL END) AS Neutral,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN id ELSE NULL END) AS Positive
FROM
  titles
WHERE
  date BETWEEN '2009-01-01' AND '2023-12-31'
GROUP BY
  Category
ORDER BY 
  Category ASC

```

<DataTable data={sum_by_polarity} rows={20}>
    <Column id="Category" title="Category" />
    <Column id="Negative" title="Negative" contentType=colorscale scaleColor=red/>
    <Column id="Neutral" title="Neutral" contentType=colorscale scaleColor=grey/>
    <Column id="Positive" title="Positive" contentType=colorscale scaleColor=green/>
</DataTable>

## Number of students with NEGATIVE experiences (2022-2023)
```sql negative_reviews_2022
-- For the year 2022
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN id ELSE NULL END) AS negative_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2022-01-01' AND '2022-12-31'AND
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
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN id ELSE NULL END) AS negative_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2023-01-01' AND '2023-12-31'AND
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

## Number of students with POSITIVE experiences (2022-2023)
```sql positive_reviews_2022
-- For the year 2022
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN id ELSE NULL END) AS positive_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2022-01-01' AND '2022-12-31'AND
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
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN id ELSE NULL END) AS positive_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2023-01-01' AND '2023-12-31'AND
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

