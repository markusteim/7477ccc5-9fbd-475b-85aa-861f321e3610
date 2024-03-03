# Summary

Overall **<Value data={polarity_proportions} column=percentage row=2/>%**  of customers had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** of customers who had a **negative experience**.


**Negative** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN TRIM(LOWER(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN TRIM(LOWER(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'visual'
    GROUP BY
        CASE
            WHEN TRIM(LOWER(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN TRIM(LOWER(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END
),
TotalReviews AS (
    SELECT
        SUM(category_count) AS total
    FROM
        MergedCategoryCounts
),
CategoryTemplate AS (
    SELECT 'positive' AS Category
    UNION ALL SELECT 'negative'
    UNION ALL SELECT 'neutral'
)
SELECT
    ct.Category,
    COALESCE(mcc.category_count, 0) AS category_count,
    COALESCE(ROUND((mcc.category_count * 100.0) / tr.total, 2), 0) AS percentage
FROM
    CategoryTemplate ct
LEFT JOIN MergedCategoryCounts mcc ON ct.Category = mcc.CleanCategory
CROSS JOIN TotalReviews tr
ORDER BY
    ct.Category
```


```sql sum_by_polarity
WITH PolarityCounts AS (
    SELECT
        LOWER(TRIM(polarity)) AS Polarity,
        COUNT(DISTINCT review_id) AS Polarity_sum
    FROM
        hotels.titles
    WHERE travel_date BETWEEN '2022-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'visual'
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
    colorPalette={
        [
        "#FF4136", // A shade of red
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

<br>

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
    echartsOptions={{
    xAxis: {
      axisLabel: {
        rotate: 45 // Rotate labels by 45 degrees if on mobile
      }
    }
  }}
/>


<Tabs>
    <Tab label="What does NOT work">
                       <b>Summary:</b>
        <br>
        
        The feedback from guests indicates that there are specific concerns regarding the
lighting and visibility within the hotel rooms. The issues seem to revolve around the
effectiveness of the black-out curtains and the overall brightness of the rooms.
Guests have noted that the black-out curtains do not completely block out light,
particularly at the corners, allowing light to enter the room in the morning.
Additionally, there is a suggestion that the brightness of the rooms could be
improved. These points suggest that attention to the window coverings and interior
lighting could enhance guest satisfaction. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Ineffective black-out curtains (black-out curtains in my room aren&#39;t 100
percent effective, corners aren&#39;t covered meaning there&#39;s significant light still
coming in the morning).</li>
<li>Insufficient room brightness (just I think it will be better if you improve and
increase brightness of rooms).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To address the concerns raised by guests, the hotel could consider the
following improvements:</li>
<li>Assess and replace the existing black-out curtains with ones that provide
better coverage and are more effective at blocking out light, ensuring that they
fit the windows properly and cover any gaps, especially at the corners.</li>
<li>Evaluate the lighting fixtures and bulbs in the rooms to determine if they
provide adequate light. Consider adding more lighting options or using
higher-lumen bulbs to increase the brightness of the rooms to a comfortable
level for guests</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        Based on the guest reviews, the hotel seems to be doing well in terms of lighting and
visibility. Guests have described the clarity of visibility as "crystal clear," indicating
that the hotel provides an environment where guests can easily see and navigate.
The term "very positive" suggests that guests are generally satisfied with the lighting
conditions. The phrase "left it shining" could imply that the lighting left a lasting
positive impression on guests or that the maintenance of lighting fixtures is
commendable. Additionally, the mention of "light with everything we need for
comfortable staying" indicates that the hotel has successfully met the lighting needs
of its guests, contributing to a comfortable experience 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Clarity of visibility (&quot;crystal clear&quot;)</li>
<li>General satisfaction with lighting conditions (&quot;very positive&quot;)</li>
<li>Lasting positive impression of lighting (&quot;left it shining&quot;)</li>
<li>Adequate lighting for comfort (&quot;light with everything we need for comfortable
staying&quot;)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Conducting a detailed survey to gather specific feedback on lighting
preferences, such as brightness levels in different areas or the use of
energy-efficient lighting. Additionally, exploring mood lighting options in certain
areas like the lounge or dining spaces could add to the ambiance and overall
guest experience.</li>
<li>Regular maintenance checks to ensure all lighting fixtures are in optimal
condition would also be advisable.</li>


    </ol> 
    </Tab>
</Tabs>