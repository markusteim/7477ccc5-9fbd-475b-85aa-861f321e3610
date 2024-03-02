

# Summary
Overall **<Value data={polarity_proportions} column=percentage row=2/>%**  of customers had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** of customers who had a **negative experience**.


**Negative** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN LOWER(TRIM(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN LOWER(TRIM(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN LOWER(TRIM(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND LOWER(TRIM(Category)) = 'aesthetic appreciation'
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
        COUNT(DISTINCT review_id) AS Polarity_sum
    FROM
        hotels.titles
    WHERE travel_date BETWEEN '2022-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'aesthetic appreciation'
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
        "#FF4136", // A shade of red
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>

## Positive:

1. Stunning Views: Guests were captivated by the breathtaking vistas, from the panoramic balcony views
to the spectacular sights of the sea, Dubai Marina, and iconic landmarks like Burj Al Arab.
2. Modern Design: The hotel's contemporary and stylish design received high praise, with its new
facilities, fresh furniture, and modern decor creating an inviting atmosphere.
3. Luxurious Ambiance: The ambiance and decor of the hotel were described as luxurious and tastefully
done, contributing to a premium feel and a comfortable stay.
4. Exceptional Facilities: The hotel's amenities, including the infinity pool and well-equipped rooms, were
highlighted as exceptional, enhancing the overall guest experience.
5. Strategic Location: The hotel's location was lauded for its convenience and strategic proximity to
attractions, with many rooms offering stunning views of the surrounding scenery.

## Negative:

1. Construction Issues: Some guests were disappointed by the presence of construction sites, which
obstructed views and impacted the hotel's tranquility.
2. Limited Views: A few reviewers expressed discontent with their room views, expecting more
picturesque scenes rather than construction or street views.
3. Outdated Elements: Despite the hotel's modernity, there were mentions of certain areas, like the
bathrooms, needing updates or renovations.
4. Misleading Descriptions: Guests found some room descriptions misleading, particularly regarding the
promised views of landmarks or the sea.
5. Comparative Disadvantages: A sense of the hotel not living up to the standards of neighboring
properties was noted, with some guests feeling it lacked the same level of luxury or view quality.

<br>

## Most Positive Examples:

1. "balcony's panoramic view was simply stunning"
2. "room has a spectacular view on Dubai Marina and Palm Jumeirah"
3. "property is very new, which means everything is in excellent condition"
4. "rooms are of spectacular standards"
5. "amazing hotel with striking view"

 

## Most Negative Examples:

1. "view from this place was just so-so"
2. "huge building site and motorways"
3. "current view from my room and the pool was a building site"
4. "pool looks out into what looks like a war zone"
5. "you cannot see the palm all the what you see is just a street"

<br>



<br>

# Headlines and corresponding snippets from reviews 

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
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
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
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
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetic appreciation'
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
    AND TRIM(LOWER(Category)) = 'aesthetic appreciation' -- Change category as needed
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
    
/>


<Tabs>
    <Tab label="What does NOT work">
                       <b>Summary:</b>
        <br>
        
        The hotel's aesthetic appeal seems to be compromised primarily by external factors,
particularly ongoing construction activities surrounding the property. Guests have
expressed disappointment with the views from their rooms and the hotel's common
areas, such as the pool, which are often obstructed by building sites. Additionally,
there are mentions of the property itself showing signs of wear, particularly in the
bathrooms where tile wear and stains have been noted. The location of the hotel
also appears to be a point of contention, with guests finding it less than ideal due to
the proximity to construction and traffic. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Obstructed views due to construction (e.g., &quot;but city view not that good,&quot;
&quot;construction around the hotel,&quot; &quot;construction site in front of the hotel,&quot;
&quot;current view from my room and the pool was a building site&quot;).</li>
<li>Disappointment with room allocation (e.g., &quot;could have given a better view
room on 1st two days,&quot; &quot;expected better view&quot;).</li>
<li>Wear and tear in bathrooms (e.g., &quot;for a newer property, bathrooms needs
some updating with wear of tiles/stains&quot;).</li>
<li>Misleading online representation (e.g., &quot;it says 5 star hotel online but I don&#39;t
feel the same&quot;).</li>
<li>Unfavorable location of the pool and outdoor areas (e.g., &quot;infinity pool near the
reception, although it overlooked a construction site,&quot; &quot;swimming pool and
outdoor area very strangely located overlooking a traffic machine&quot;).</li>
<li>General dissatisfaction with the property&#39;s location and views (e.g., &quot;huge
building site and motorways,&quot; &quot;just a shame about the view from the pool,&quot;
&quot;location may seem strange,&quot; &quot;views from the pool is depressing&quot;).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

<li>Enhance the aesthetic appeal of rooms with obstructed views by adding
interior design elements that divert attention from the exterior.</li>
<li>Offer guests alternative amenities or compensatory perks if the views are a
primary selling point of the hotel.</li>
<li>Schedule maintenance for areas showing signs of wear, particularly in the
bathrooms, to ensure they meet the expectations of a high-standard property.</li>
<li>Update the hotel&#39;s online profile to accurately reflect the current state of the
hotel and its surroundings.</li>
<li>Reevaluate the layout and positioning of the pool and outdoor areas to
maximize the available views and minimize the impact of nearby construction.</li>
<li>Communicate transparently with guests about the temporary inconveniences
due to construction and provide information on the expected timelines for
completion.</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        The hotel has received commendable feedback regarding its aesthetics and the
quality of its facilities. Guests have praised the modern and stylish decor, the
cleanliness, and the high specification of the accommodations. The views from the
hotel are frequently highlighted as a significant positive feature, with many guests
mentioning the beautiful vistas of the city, sea, and landmarks. The hotel's location is
also well-regarded, with its proximity to attractions and the convenience it offers. The
amenities, including the infinity pools, gym, and restaurants, have been described as
amazing, brilliant, and great, indicating a high level of guest satisfaction in these
areas.

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

<li>Decor and Style (all incredibly decorated and to a very high spec, avani was
very sleek &amp; stylish, modern, contemporary interiors)</li>
<li>Views (amazing view, beautiful view of palma, balcony views of the city, great
view of the palmtree, spectacular views of the palm jumeirah)</li>
<li>Location (excellent location, ideally located, hotel is perfectly located, very
good location near jbr and marina)</li>
<li>Cleanliness (beautiful and clean, clean, new, beautiful, it’s modern and chic
and very clean)</li>
<li>Amenities (amenities were amazing, facilities are brilliant, great facilities with
gym, pool, restaurant)</li>
<li>Room Features (each room has 65 inch tv’s, rooms are very spacious with
nice furniture, studio apartment was amazing)</li>
<li>Hotel Facilities (hotel facilities was amazing, hotel has excellent facilities,
hotel has nice restaurants)</li>
<li>Accommodation Quality (beautiful accommodations, beautiful and big suites,
lovely modern decor, rooms were an exceptional standard)</li>
<li>Pool Areas (beautiful infinity pool, pool area was small but really pretty,
stunning infinity pool)</li>
<li>Unique Touches (creative cleaning with the beautiful birds from the towels and
some fruits, with a hint of arabesque decor that completes the ramadan feel)</li>

    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>No suggestions for this satisfaction category.</li>


    </ol> 
    </Tab>
</Tabs>


 