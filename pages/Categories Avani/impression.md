

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
    AND TRIM(Category) = 'impression'
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

<br>


```sql sum_by_polarity
WITH PolarityCounts AS (
    SELECT
        LOWER(TRIM(polarity)) AS Polarity,
        COUNT(DISTINCT review_id) AS Polarity_sum
    FROM
        hotels.titles
    WHERE travel_date BETWEEN '2022-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'impression'
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

1. Exceptional Service: Guests praised the staff and services, with special mentions of the Avani app and
the superb service in Dubai.
2. Dining Delights: The Seven Seeds restaurant received accolades for its lovely iftar and memorable
dining experiences.
3. High Recommendations: Many guests rated their experience 10/10, with strong endorsements for the
hotel's service, facilities, and overall stay.
4. Loyal Guests: Repeat visitors described their stays as memorable, with one guest staying for over two
years and still counting.
5. General Satisfaction: Guests consistently mentioned perfect stays, amazing experiences, and the hotel
meeting all expectations.
 

## Negative:

1. Service Improvement: A few guests suggested improvements in service speed and quality.
2. Health and Safety: There were concerns about lost items and the well-being of guests.
3. Room Issues: Some guests faced issues with room reassignments and inadequate room services.
4. Transportation Concerns: The hotel's drop-off area was noted to be small and often crowded.
5. Negative Experiences: A small number of guests had negative experiences, with one stating that Avani
"ruined it" for them.

<br>

## Most Positive Examples:

1. "Everything was flawlessly organized"
2. "Deeply moved"
3. "Perfect place to spend holidays."
4. "Farewelling us at our taxi"
5. "Only regret was that didn’t stay longer"

 

## Most Negative Examples:

1. "Very bad."
2. "Avani ruined it that bad for us."
3. "Make the stay there terrible."
4. "Worst experiences."
5. "I do not recommend this place."

<br>





<br>

## Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impression'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impression'
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
WHERE TRIM(LOWER(Category)) = 'impression'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impression'
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
WHERE TRIM(LOWER(Category)) = 'impression'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impression'
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
        
        The feedback from guests indicates a range of issues that affect their overall
impression of the hotel. Some guests have encountered problems with their rooms,
while others have expressed dissatisfaction with specific services or amenities.
Comparisons with other hotels suggest that guests have higher expectations based
on the price point and perceived value. There are also mentions of personal losses
on the property and during transportation, which may reflect on the security
measures or assistance provided by the hotel. The presence of both positive and
negative comments about staff and services suggests inconsistency in the guest
experience. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Room issues (01st room had a few issues, issue in our room, minor flaws that
appear as quite unnecessary).</li>
<li>Comparatively lower value (atlantis the royal and park hyatt which are both 5
stars as well. but it&#39;s totally not comparable, both price are the same, around
$2000 but now i regretted).</li>
<li>Small drop-off area (drop off area is small, often crowded with cars).</li>
<li>Lack of 24-hour laundry service (only wish they have 24 hours laundry
service).</li>
<li>Inconvenient location for some guests (it&#39;s a long drive from al ain).</li>
<li>Negative experiences leading to loss of personal items (i dropped some
valuable items in a cab, one time i lost my precious pendant after breakfast,
we lost our wallet).</li>
<li>Inconsistency in service quality (unlike the other valets, these two guys
mastered this also, very good hotels provide also shadow housekeeping
service).</li>
<li>Language barrier in accessing reviews (trip advisor system is doubtful for
japanese customers because they cannot see japanese reviews unless they
change the language to japanese).</li>
<li>Negative overall experiences (i do not recommend this place, make the stay
there terrible, very bad, worst experiences).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To address these issues, the hotel could conduct a thorough review of room
quality and address any recurring problems.</li>
<li>Improving the drop-off area to accommodate more vehicles could alleviate
congestion. Implementing a 24-hour laundry service would cater to guests&#39;
needs around the clock.</li>
<li>The hotel could also consider offering transportation services for guests who
find the location inconvenient.</li>
<li>Enhancing security measures and providing better support for lost items
would improve guest confidence.</li>
<li>Training staff to ensure consistent service quality across all roles is essential.</li>
<li>Finally, ensuring that the hotel&#39;s reviews are accessible in multiple languages
would help international guests make informed decisions.</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        Based on the guest reviews, the hotel appears to be performing well in several
areas. Guests have expressed satisfaction with the room amenities, particularly the
kitchen facilities which allow for simple meal preparation. The presence of new
utensils and appliances such as dishwashers and washing machines adds to the
convenience for guests, especially those on extended stays. The hotel's location,
with a convenience store right in front, is appreciated for its accessibility. The
availability of different room types, including apartment rooms and suites with
multiple bedrooms, caters to a variety of guests, from families to business travelers.
The hotel's breakfast offerings and the quality of food at the restaurant have received
positive remarks. The staff, including specific mentions of individuals, has been
acknowledged for their service, suggesting a personalized touch to guest
interactions. The hotel's facilities, such as the pool and gym, although not used by
all, are noted as decent. Overall, the hotel seems to be meeting guests' needs
effectively, with many expressing a desire to return or recommend the hotel to
others 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

<li>Room amenities and convenience for extended stays (e.g., &quot;able to make a
simple dinner in the kitchen in the room,&quot; &quot;all utensils were new,&quot; &quot;diswasher
and washing machine&quot;).</li>
<li>Location and accessibility (e.g., &quot;convenience store right in front of the hotel&quot;).</li>
<li>Variety of room types suitable for different guests (e.g., &quot;big appartement with
two sleeping rooms,&quot; &quot;apartment room on the 16th floor&quot;).</li>
<li>Quality of food and breakfast offerings (e.g., &quot;breakfast was as you would
expect from a 5-star hotel,&quot; &quot;food was to a high standard&quot;).</li>
<li>Staff service and recognition (e.g., &quot;asim s. &amp; dana o,&quot; &quot;david,&quot; &quot;simran&quot;).</li>
<li>Hotel facilities (e.g., &quot;at the pool,&quot; &quot;gym n/a. didn’t use, but looked decent&quot;).</li>

    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To further enhance the guest experience, the hotel could consider expanding
the use of its facilities by encouraging guests to try them out, perhaps through
promotional activities or guided tours. Additionally, ensuring that all staff
members are equally recognized and known by guests could further
personalize the service.</li>
<li>While the hotel is performing well, continuous monitoring of guest feedback
for any emerging issues or areas for improvement will help maintain high
standards of service.</li>


    </ol> 
    </Tab>
</Tabs>