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
    AND TRIM(Category) = 'safety'
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
    AND LOWER(TRIM(Category)) = 'safety'
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
/>

<br> 

## Positive:

1. Secure Environment: Guests felt a strong sense of security, with one noting the area was "super
secure" and another expressing confidence in leaving their home secure during visits.
2. Taxi Safety: The staff's commitment to ensuring guests' safety in taxis was highlighted, with multiple
mentions of staff assisting with safe transportation.
3. Family Security: The hotel's night security team received praise for creating a secure environment,
particularly for families and young children, with guests feeling comfortable leaving belongings out.
4. Amenities and Service: The presence of lifeguards at the pool, efficient elevator service, and the
thoroughness of housekeeping staff like Sup.Mynul and Madhu contributed to guests' peace of mind.
5. General Safety: Overall, guests felt very safe at the hotel, with one mentioning the constant pool
depth as a safety feature and another appreciating the non-crowded spaces.
 

## Negative:

1. Isolated Location: The only negative comment was about the hotel's relatively isolated location, which
could imply safety concerns for some guests.

<br>

## Most Positive Examples:

1. "area was super secure"
2. "staff ensure you get in a safe taxi and know where they are taking you"
3. "ensuring a secure environment for young children and families"
4. "they are so trustworthy and extremely reliable"
5. "amazing lifeguards"

## Most Negative Examples:

1.  "relatively isolated location"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
    AND TRIM(LOWER(Category)) = 'fun & stress-free' -- Change category as needed
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


<Tabs>
    <Tab label="What does NOT work">
                       <b>Summary:</b>
        <br>
        
        The feedback from guests indicates concerns regarding the hotel's location and the
presence of building work. The term "relatively isolated location" suggests that
guests may feel the hotel is situated in an area that is not well-populated or close to
other establishments, which can lead to a sense of unease, especially during odd
hours or for those unfamiliar with the area. The mention of "undertaken any building
work" implies there is construction activity on-site or nearby, which can be perceived
as a security concern due to the presence of construction workers and potential
hazards associated with construction sites. The phrase "very careful" could be
interpreted as guests feeling the need to be extra vigilant during their stay, which
may detract from their overall comfort and experience at the hotel. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Isolated location leading to safety concerns (relatively isolated location).</li>
<li>Presence of building work causing unease (undertaken any building work).</li>
<li>Guests feeling the need to be extra vigilant (very careful)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To address these concerns, the hotel management could enhance security
measures, such as increasing surveillance and patrolling, especially during
the evening and night.</li>
<li>Clear communication with guests about the nature and expected duration of
building work can help manage expectations and alleviate concerns.</li>
<li>If possible, the hotel could also provide information about the area, including
safety tips and available amenities, to help guests feel more at ease.</li>
<li>Additionally, ensuring that construction areas are well-lit and securely fenced
off can minimize perceived risks associated with the building work.</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        Based on the provided snippets, guests at the hotel have expressed satisfaction with
the safety and security measures in place. The presence of a diligent security team,
both during the day and at night, has been highlighted as a positive aspect.
Lifeguards are commended for their vigilance, contributing to a safe pool
environment. The hotel's infrastructure, including elevators and parking, is noted for
functioning efficiently and contributing to the overall sense of security. Guests have
also mentioned the comfort they feel in leaving their belongings unattended,
indicating trust in the hotel staff. The hotel's efforts to ensure a secure environment
for families and the reliable valet service have also been well-received. 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Attentive security team (e.g., &quot;great team on security at night&quot;, &quot;security team
did an amazing job&quot;)</li>
<li>Trustworthy staff (e.g., &quot;they are so trustworthy and extremely reliable&quot;)</li>
<li>Safe environment for families (e.g., &quot;ensuring a secure environment for young
children and families&quot;)</li>
<li>Efficient infrastructure (e.g., &quot;elevators worked perfectly&quot;, &quot;four elevators work
quickly and efficiently&quot;)</li>
<li>Reliable valet service (e.g., &quot;valet service of this is very good&quot;)</li>
<li>Vigilant lifeguards (e.g., &quot;amazing lifeguards&quot;, &quot;lifeguards&quot;)</li>
<li>Secure parking (e.g., &quot;parking of the hotel was also comfy&quot;)</li>
<li>Safe pool area (e.g., &quot;pool was safety and nice&quot;, &quot;pool depth was a constant
1.2m&quot;)</li>
<li>Guest belongings safety (e.g., &quot;i felt comfortable to leave my belongings out in
the open with them&quot;)
10.Safe transportation arrangements (e.g., &quot;staff at the hotel ensure you get in a
safe taxi&quot;)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To further enhance the guest experience, the hotel could consider
implementing regular safety briefings for guests upon check-in, providing
information on security protocols and emergency procedures. Additionally, the
hotel might explore the possibility of introducing a guest feedback system
specifically for safety and security, allowing for real-time improvements and
adjustments.</li>


    </ol> 
    </Tab>
</Tabs>