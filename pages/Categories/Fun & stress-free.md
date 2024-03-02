
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
    AND TRIM(Category) = 'fun & stress-free'
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
    AND LOWER(TRIM(Category)) = 'fun & stress-free'
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
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>


## Positive:

1. Location Convenience: Guests praised the hotel's proximity to tram and monorail stations, making
travel around Dubai easy.
2. Transport Access: The hotel's location was appreciated for its easy access to public transportation,
including the metro.
3. Amenities Enjoyment: Some guests enjoyed the hotel's amenities, like the pool and gym, and found
them to be great for children.
4. Room Features: A few reviews mentioned well-equipped kitchens and the convenience of having TVs
in every room.
5. Efficient Services: Positive remarks were made about the smooth check-in and check-out process, and
the helpfulness of the travel desk.
 

## Negative:

1. Limited Entertainment: Guests felt the area lacked nightlife and socializing options, with no bars or
nearby hangout spots.
2. Accessibility Issues: Some found the hotel's location inconvenient, with nothing within walking
distance and difficulties in reaching the hotel by car.
3. Amenities Shortcomings: Complaints about the pool size, lack of sun loungers, and limited TV channel
selections were noted.
4. Service Delays: There were mentions of delays in service and slow responses to requests, impacting
guest satisfaction.
5. Connectivity Concerns: Issues with Wi-Fi speed and connectivity were a point of frustration for some
guests.

<br>

## Most Positive Examples:

1. "great transport links"
2. "easy access to metro and tram"
3. "hotel is well connected to the public transport tram metro"
4. "location being a very short walk to the tram station"
5. "tram station right in front of the hotel"


## Most Negative Examples:

1. "hotel has no-where to hang out"
2. "location wasn't great"
3. "swimming pool was affected by the problem"
4. "tv selections in the room is very limited"
5. "very few staff members available during breakfast"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
        
        The guest reviews indicate that the hotel's location and amenities present challenges
for guests seeking a fun, vibrant, and stress-free experience. The primary concerns
revolve around the hotel's accessibility, both in terms of its physical location and the
ease of reaching nearby attractions. Guests have expressed difficulties with
transportation, including the complexity of driving to the hotel and the distance from
public transit options. Additionally, the hotel's on-site facilities, such as the pool and
social spaces, seem to be limited or not meeting guest expectations. Technical
issues with devices and Wi-Fi connectivity also contribute to a less than optimal stay

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Repetitive device activation process (&quot;all devices had to be activated with the
same information over and over again&quot;).</li>
<li>Slow area around the hotel (&quot;area is a bit slow&quot;).</li>
<li>Complicated access by car (&quot;driving to the hotel and out is like a maze&quot;).</li>
<li>Lack of hangout spaces (&quot;hotel has no-where to hang out&quot;).</li>
<li>Distance from public transportation (&quot;it takes approximately 20 minutes by
walking to the nearest metro station&quot;).</li>
<li>Travel time to downtown (&quot;it will take more than 45 minutes to reach down
town&quot;).</li>
<li>Isolated location (&quot;location is near nothing, you have to take a cab to
anywhere&quot;).</li>
<li>Limited nearby attractions (&quot;not much to do or see in the area of the hotel&quot;).</li>
<li>Lack of nearby alternative relaxation spots (&quot;nowhere in walking distance
where one can go and chill&quot;).</li>
<li>Limited sun exposure at the pool (&quot;only criticism is due to its layout the sun
leaves the pool at 2!&quot;).</li>
<li>Lack of local activities and dining options (&quot;only problem i had was there is not
much within the local area to do or eat&quot;).</li>
<li>Absence of a bar for socializing (&quot;only reason is the lack of a bar to socialise
in&quot;).</li>
<li>Need for nighttime activities (&quot;only suggestion is that there should be activities
at night&quot;).</li>
<li>Service delays (&quot;only thing i might complain about is delays in services and
responses to our requests&quot;).</li>
<li>Small and busy pool (&quot;pool is quite small and very busy&quot;).</li>
<li>Limited pool hours (&quot;pool therefore only really can be enjoyed after 5pm&quot;).</li>
<li>Insufficient power outlets (&quot;power plugs&quot;).</li>
<li>Slow room preparation (&quot;preparing our twin beds and else... took longer than
usual&quot;).</li>
<li>Early closure of facilities (&quot;should be open longer than 10pm&quot;).</li>
<li>Limited TV channel selection (&quot;tv selections in the room is very limited &#39;sport channels are poor&#39;&quot;).</li>
<li>Understaffing during breakfast (&quot;very few staff members available during
breakfast&quot;).</li>
<li>Confusing lobby location (&quot;we&#39;re quite confused though upon arriving as the main lobby was situated in the first floor&quot;).</li>
<li>Slow Wi-Fi (&quot;wifi was not the fastest&quot;).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To improve the guest experience, the hotel could streamline the device
activation process and enhance Wi-Fi connectivity.</li>
<li>Addressing transportation issues by providing clear directions, shuttle
services, or partnerships with local transit could alleviate concerns about the
hotel&#39;s location.</li>
<li>Expanding on-site amenities, such as increasing the number of sun loungers,
extending facility hours, and adding a bar or social area, would offer guests
more options for relaxation and socializing.</li>
<li>Organizing activities, especially in the evening, could also enhance the
vibrancy of the hotel. Improving the speed of services and ensuring adequate
staffing during peak times would further contribute to a stress-free
environment for guests.</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        The hotel's strategic location appears to be a significant asset, as evidenced by the
numerous mentions of its proximity to public transportation options such as the tram,
monorail, and metro. Guests appreciate the ease of access to various parts of the
city, including tourist attractions, business districts, and recreational areas. The
check-in and check-out processes are consistently described as smooth and
efficient, contributing to a stress-free experience. The availability of well-equipped
kitchens in the rooms is also highlighted, offering guests the convenience of home
comforts. The hotel's connection to local activities and the presence of family-friendly
amenities like pools and entertainment options seem to enhance the overall guest
experience. 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Proximity to public transportation (e.g., &quot;tram station right in front of the hotel&quot;,
&quot;very close to metro&quot;, &quot;hotel is connected to public transpo such as the tram
and monorail&quot;)</li>
<li>Efficient check-in/check-out process (e.g., &quot;check in was quick and easy&quot;,
&quot;super fast check out&quot;, &quot;early check-in was free of charge&quot;)</li>
<li>Well-equipped kitchens (e.g., &quot;kitchen in the room was well equipped with all
you need&quot;, &quot;you have a washing machine and a fully equipped kitchen&quot;)</li>
<li>Family-friendly amenities (e.g., &quot;family pool was great for children&quot;, &quot;kids love
the pool and breakfast buffet&quot;, &quot;there is also a kid’s pool and a hot tub&quot;)</li>
<li>Location conducive to tourism and business (e.g., &quot;good location not
expensive to get to from DXB&quot;, &quot;convenient if you need to be close to internet
/ media city&quot;, &quot;hotel is not far from most attractions&quot;</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Additionally, ensuring that guests are well-informed about the local
transportation options and any ongoing construction that might affect
accessibility could improve convenience.</li>
<li>The hotel might also explore partnerships with local attractions to offer
exclusive deals or packages, adding value to the guests&#39; stay.</li>


    </ol> 
    </Tab>
</Tabs>