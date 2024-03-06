```sql summaries
 select * from hotels.summaries 
 ```

# Summary
Overall **<Value data={polarity_proportions} column=percentage row=2/>%**  of students had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** of students who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the university reviews regarding the sense of belonging, care, and support is predominantly positive. Students frequently express feelings of being welcomed, supported by faculty, and finding their community within various groups and organizations. However, there are instances of negative experiences where students felt isolated, uncared for, or found the social atmosphere lacking.

```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN LOWER(TRIM(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN LOWER(TRIM(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN LOWER(TRIM(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT id) AS category_count
    FROM
        hotels.titles
    WHERE date >= '2009-01-01' AND date <= '2023-12-31'
    AND LOWER(TRIM(Category)) = 'aesthetics'
    GROUP BY
        CASE
            WHEN LOWER(TRIM(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN LOWER(TRIM(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN LOWER(TRIM(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END
),
TotalReviews AS (
    SELECT
        SUM(category_count) AS total
    FROM
        MergedCategoryCounts
)
SELECT
    COALESCE(mcc.CleanCategory, 'other') AS Category,
    COALESCE(mcc.category_count, 0) AS category_count,
    COALESCE(ROUND((mcc.category_count * 100.0) / tr.total, 2), 0.00) AS percentage
FROM
    (SELECT 'positive' AS Category UNION ALL SELECT 'negative' UNION ALL SELECT 'neutral') ct
LEFT JOIN MergedCategoryCounts mcc ON ct.Category = mcc.CleanCategory
CROSS JOIN TotalReviews tr
ORDER BY
    Category
```

```sql sum_by_polarity
WITH PolarityCounts AS (
    SELECT
        LOWER(TRIM(polarity)) AS Polarity,
        COUNT(DISTINCT id) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'aesthetics'
    GROUP BY
        LOWER(TRIM(polarity))
)
SELECT
    Polarity,
    Polarity_sum
FROM PolarityCounts
ORDER BY
    CASE Polarity
        WHEN 'very negative' THEN 1
        WHEN 'negative' THEN 2
        WHEN 'neutral' THEN 3
        WHEN 'positive' THEN 4
        WHEN 'very positive' THEN 5
    END
```

<BarChart 
    data={sum_by_polarity} 
    swapXY=false
    x=Polarity
    y=Polarity_sum 
    series=Polarity
    sort=false
    title="Distribution of customer sentiment"
    colorPalette={
        [
        "#85144B", // A shade of dark red
        "#FF4136", // A shade of red
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
    echartsOptions={{
    xAxis: {
      axisLabel: {
        rotate: 45 // Rotate labels by 45 degrees if on mobile
      }
    }
  }}
/>



## Positive:
- **Community Building:** Students often find their place within the university through clubs, Greek life, and residence halls, creating tight-knit communities and meaningful friendships.
- **Faculty Engagement:** Many professors are praised for being approachable, responsive, and genuinely caring about students' well-being, often going beyond academic support.
- **Diverse Inclusion:** The university's diverse student body and faculty contribute to an environment where different backgrounds are celebrated, fostering a sense of belonging for international and minority students.
- **Academic Support:** There is a strong emphasis on academic assistance, with numerous resources available on campus, and professors who are willing to help students understand course material.
- **Personal Growth:** The university environment encourages personal and professional connections, internships, and a broader perspective, contributing to students' growth and future opportunities.


## Negative:
- **Social Challenges:** Some students experience feelings of isolation, difficulty fitting in, or find the social scene lacking, particularly if they are not part of Greek life or specific social groups.
- **Faculty Indifference:** A few students report encounters with faculty who seem apathetic or uninterested in student success, contrasting with the otherwise positive faculty reviews.
- **Financial Struggles:** There are mentions of the university not being accommodating to students facing financial difficulties, affecting their sense of support.
- **Exclusivity Issues:** Negative experiences include feeling unwelcome in certain groups, facing discrimination, or dealing with a competitive atmosphere that hinders inclusivity.
- **Administrative Barriers:** Some students find the administration unhelpful or bureaucratic, which can detract from the sense of community and support.


<br>

## Most Positive Examples:

- "people here really care for you"
- "you'll get friends no matter what"
- "professors were responsive to online school changes"
- "students and profs are kind and welcoming"
- "i have a more worldly and humanitarian perspective"


## Most Negative Examples:

- "faculty don't really care about its students"
- "you will not fit in"
- "they don't seem to care much about the students"
- "i feel like everyone is not friendly or stingy"
- "not student centric or accommodating to those with financial struggles"

<br>



<br>

# Headlines and corresponding snippets from reviews 

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
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
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
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
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
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

# Student sentiment distribution (2020-2023)

```sql sentiment_distribution
WITH Polarity_Ordered AS (
  SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    Year, -- Extract the year from the date
    COUNT(DISTINCT id) AS ReviewCount, -- Count unique review IDs
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
    date BETWEEN '2020-01-01' AND '2023-12-31'
    AND TRIM(LOWER(Category)) = 'aesthetics' -- Change category as needed
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
    sort = false
    echartsOptions={{
    xAxis: {
      axisLabel: {
        rotate: 45 // Rotate labels by 45 degrees if on mobile
      }
    }
  }}
/>
