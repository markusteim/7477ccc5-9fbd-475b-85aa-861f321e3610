


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
    AND TRIM(Category) = 'sound'
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
    AND LOWER(TRIM(Category)) = 'sound'
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
/>

 <br>

## Positive:

1. Soundproofing Success: Guests praised the effective soundproofing, noting that even rooms facing the
road remained undisturbed.
2. Silent AC: The air conditioning was commended for its silent operation, contributing to a clean and
quiet room atmosphere.
3. Insulated Interiors: Many found the rooms to be very quiet, with excellent sound insulation ensuring a
peaceful environment.
4. Audio Amenities: In-room audio features like Bluetooth speakers and smart TVs enhanced the
enjoyment of personal music without noise issues.
5. Construction Immunity: Despite nearby construction, guests reported a quiet and undisturbed stay,
with no impact on their comfort.
 

## Negative:

1. Room Shortcomings: Some guests mentioned low TV volume and curtains that didn't close properly,
affecting room comfort.
2. Noise Nuisances: Complaints included construction noise, loud fridges, and noisy housekeeping in the
corridors.
3. Disturbance Displeasure: Guests were disturbed by demolition sounds, road noise, and parties in
adjacent rooms.
4. Unwanted Wake-ups: Early morning construction and inadequate curtains led to guests being woken
up too early.

<br>

## Most Positive Examples:

1. "windows faced the road but they were soundproof"
2. "air conditioning was working perfectly and silently"
3. "rooms have excellent sound insulation"
4. "bluetooth speaker"
5. "noiseless no disturbances"

 
## Most Negative Examples:

1. "construction noise from the building next door"
2. "firdge is very loud"
3. "noise caused by the demolition of a building around the hotel"
4. "outside noise"
5. "rooms are not as quiet"

<br>



# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sound'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sound'
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
WHERE TRIM(LOWER(Category)) = 'sound'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sound'
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
WHERE TRIM(LOWER(Category)) = 'sound'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```



```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sound'
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
        
        The hotel is currently experiencing a range of sound-related issues that are affecting
guest comfort. These issues stem from both external and internal sources.
Externally, guests are disturbed by construction noise from nearby sites, including
concrete breaking and drilling, which is particularly disruptive by the pool area and
for those facing the road. Internally, guests have noted that the rooms are not
sufficiently soundproofed, leading to disturbances from housekeeping activities in the
corridors, loud refrigerators, and noise from neighboring rooms, especially during
parties. Additionally, some guests have reported that the TV volume is too low and
that the music played within the hotel premises is not to their liking. There are also
comments about the curtains not closing properly, allowing sunlight to enter the
rooms early in the morning. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Construction noise from nearby sites (construction going on right opposite the
hotel pool, construction noise from the building next door, noise caused by the
demolition of a building around the hotel, shame about the concrete breaking
on the building site opposite).</li>
<li>Maintenance and construction within the hotel (currently there are some
maintenance and constructions happening as of September 19th, 2022).</li>
<li>Inadequate soundproofing in rooms (rooms are not as quiet, there can
sometimes be parties in the adjacent hotel rooms on the weekends which are
pretty noisy).</li>
<li>Noise from hotel operations (housekeeping was quite noisy in the corridor).</li>
<li>Disturbances starting early in the morning (it started at 6am until late
afternoon).</li>
<li>Loud refrigerator in rooms (fridge is very loud).</li>
<li>Inadequate curtain functionality (hotel management to ensure that the curtains
close all the way, don&#39;t let the sun wake me at a too early hour).</li>
<li>Displeasure with music played in the hotel (dodgy music).</li>
<li>Low TV volume (tv volume was low).
10.General external noise issues (outside noise, road is noisy, only downside is
the current works going on over the road which makes poolside less relaxing,
it was quite a riot time to time out there, so not great with constant concrete
breakers going).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To address these issues, the hotel could consider the following improvements:</li>
<li>Install better soundproofing materials in the rooms and corridors to minimize
internal noise.</li>
<li>Coordinate with construction companies to limit the hours of operation to less
disruptive times.</li>
<li>Provide guests with advance notice of any planned maintenance or
construction work within the hotel.</li>
<li>Review and adjust the music playlist to better suit the guest demographic.</li>
<li>Ensure that all curtains in guest rooms are fully functional and block out light
effectively.</li>
<li>Check and repair or replace noisy refrigerators in guest rooms.</li>
<li>Adjust the TV systems to allow for higher volume settings.</li>
<li>Implement a policy to manage noise levels from parties in hotel rooms,
possibly including designated quiet hours or areas.
10.Consider additional measures to mitigate road noise for rooms facing the
street, such as secondary glazing.</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        The hotel seems to be managing sound-related issues effectively, as evidenced by
the guest reviews. Despite the presence of construction work nearby, guests have
reported that it did not affect their stay, indicating that the hotel has taken measures
to mitigate external noise. The reviews frequently mention the soundproofing of
rooms, which suggests that the hotel has invested in infrastructure to ensure a quiet
environment. Additionally, the availability of Bluetooth speakers and smart TVs with
music capabilities enhances the guests' personal experience. The hotel's location in
a quiet area and the soundproof windows further contribute to a peaceful
atmosphere. The air conditioning system is also noted for its silent operation, adding
to the overall comfort of the guests. 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Effective soundproofing (&quot;all rooms are soundproof&quot;, &quot;rooms have excellent
sound insulation&quot;, &quot;windows faced the road but they were soundproof&quot;)</li>
<li>Quiet location (&quot;hotel located in nice quiet area&quot;, &quot;it is quiet&quot;, &quot;quietness&quot;)</li>
<li>Minimal disturbance from external construction (&quot;even with some construction
work going on across the road, this did not impact our time at all&quot;, &quot;yes there
are some building works nearby but this did not intrude on our stay
whatsoever&quot;)</li>
<li>In-room entertainment options (&quot;Bluetooth speaker&quot;, &quot;i was able to be
comfortable and listen to my music on the smart tv&quot;, &quot;great music&quot;)</li>
<li>Silent air conditioning (&quot;air conditioning was working perfectly and silently&quot;)</li>
<li>General quiet atmosphere in the hotel (&quot;quiet&quot;, &quot;room quiet&quot;, &quot;rooms are also
very quiet&quot;, &quot;rooms are silent and clean&quot;, &quot;restaurant was comparatively
quiet&quot;)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>No suggestions for this satisfaction category.</li>


    </ol> 
    </Tab>
</Tabs>