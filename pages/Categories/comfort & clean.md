

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
    AND TRIM(Category) = 'comfort & clean'
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
    AND LOWER(TRIM(Category)) = 'comfort & clean'
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



## Positive:

1. Room Quality: Guests raved about the luxurious, spacious rooms with comfortable beds and
high-quality linen, ensuring restful nights.
2. Amenities Galore: Many appreciated the well-equipped rooms with modern appliances, including
washing machines, and the plush toiletries provided.
3. Pristine Cleanliness: The cleanliness of the rooms received high praise, with housekeeping services
lauded for their diligence and consistency in maintaining spotlessness.
4. Facility Excellence: The hotel's facilities, such as the gym and pool, were described as excellent, with
special mentions of the heated infinity pool and the well-maintained amenities.
5. Comfortable Accommodations: The generously sized accommodations were highlighted for their
comfort, with guests enjoying the ample space and the cozy, home-like atmosphere.

## Negative:

1. Room Discomfort: A few guests found the air conditioning inadequate and the rooms or pool area too
small, leading to a cramped experience.
2. Service Shortcomings: Some guests noted issues with housekeeping, such as infrequent cleaning or
the need for better maintenance in certain areas.
3. Environmental Factors: There were mentions of the hot weather affecting comfort levels, with guests
needing to shower frequently to cool down.
4. Amenity Issues: A minority reported minor inconveniences like leaky showers, dirty windows, and a
lack of towel dryers.
5. Safety Hazards: One guest mentioned slipping on a hard marble floor, indicating a potential safety
concern.

<br>

## Most Positive Examples:

1. "bed and bedding were of the highest quality"
2. "cleaning was fabulous"
3. "pillows here are heavenly to sleep on"
4. "room was sufficiently quiet, clean and amazing"
5. "they were shining each day"

 

## Most Negative Examples:

1. "air-conditioning in the room was quite weak"
2. "only let down is rooms are bit cramped"
3. "i saw spots on the table that were not cleaned up"
4. "water leaking through the shower door"
5. "i flipped once on a hard marble floor"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
Customer sentiment distribution (2022-2023)

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
    AND TRIM(LOWER(Category)) = 'comfort & clean' -- Change category as needed
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
        
        The guest reviews indicate a series of specific concerns related to comfort and
cleanliness at the hotel. Issues range from the functionality and adequacy of the air
conditioning system to the size and amenities of the pool area. Guests have also
noted the size constraints of the rooms and bathrooms, as well as the limited
availability of beds for the number of guests. Cleanliness concerns were raised
regarding the state of the windows, under the bed, and the maintenance of the
bathroom fixtures. Additionally, there are mentions of inconveniences such as the
lack of a clothes dryer, the need for more towel replacements, and the impracticality
of certain room features like the makeup mirror. While some guests found the pool
nice, others felt it was too small or lacked depth variation. These snippets provide a
focused insight into specific areas that could be improved to enhance guest
satisfaction.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Inadequate air-conditioning (air-conditioning in the room was quite weak).</li>
<li>Uncomfortable makeup mirror design (stupid makeup mirror next to the tv,
which was curved).</li>
<li>Limited sun exposure in the pool area (area goes into shade around 1430; by
the afternoon the pool and loungers were all shaded).</li>
<li>Small bathroom size (bathroom a little small).</li>
<li>Insufficient bedding for guests (beds limited for the number of guests).</li>
<li>Mediocre pool facilities (didn&#39;t go to the pool, it was mediocre).</li>
<li>Frequent need for showers due to hot weather (had to take a shower after
every single schedule).</li>
<li>Requirement for daily fresh towels (have to use new towel each day).</li>
<li>Suggestions for pool improvements (i also feel like pool should be bigger or
maybe there should be a certain part of the pool that is deep).</li>
<li>Under-bed cleanliness (in a lot of four and five star hotels when i bent down to
check under the bed it was dirty).</li>
<li>Lack of amenities for hair appliances (including one by a mirror for our hair
dryers / straighteners ladies).
<li>Absence of a clothes dryer (missing thing no towels or clothes dryer).</li>
<li>Cramped rooms and bathrooms (only let down is rooms are bit cramped and
so are the bathrooms).</li>
<li>Malfunctioning bedroom door (one of the bedroom doors were impossible to
be closed).</li>
<li>Small pool size (pool is nice, but a bit small; pool is small).</li>
<li>Bathroom maintenance issues (some covers were off in the bathrooms).</li>
<li>Restaurant temperature discomfort (temperature a bit too chilly in the
restaurant).</li>
<li>Ineffective air conditioning temperature control (temperature of the ac was
bad).</li>
<li>Inadequate pillow options (two kinds of pillows provided, both of them were
soft).</li>
<li>Shower leakage (water leaking through the shower door).</li>
<li>Frequent laundry due to weather (we had to wash his clothes 1-2 times/day).</li>
<li>Dirty windows (windows in the rooms were really dirty).</li>
<li>Unaddressed spills (i saw spots on the table that were not cleaned up).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <ol style="list-style-type: decimal; margin-left: 20px;">
<li>To address these issues, the hotel management could consider the following
improvements:</li>
<li>Assess and upgrade the air conditioning system to ensure it provides
adequate cooling.</li>
<li>Replace or reposition the makeup mirror for better functionality.</li>
<li>Investigate ways to extend sun exposure in the pool area, possibly with
retractable shades.</li>
<li>Review room and bathroom sizes and consider renovations to optimize
space.</li>
<li>Ensure an adequate supply of beds and bedding to accommodate the number
of guests.</li>
<li>Enhance the pool area with size expansion or depth variation to meet guest
expectations.</li>
<li>Implement a more flexible towel replacement policy to accommodate guest
needs.</li>
<li>Conduct thorough cleaning checks under beds and in other overlooked areas.</li>
<li>Install additional outlets or amenities for hair appliances.</li>
<li>Provide a clothes drying solution for guests.</li>
<li>Repair or replace malfunctioning doors and bathroom fixtures.</li>
<li>Adjust the restaurant temperature to a more comfortable level.</li>
<li>Review and improve the air conditioning temperature control system.</li>
<li>Offer a variety of pillow types to cater to different guest preferences.</li>
<li>Fix any issues causing water leakage in showers.</li>
<li>Increase the frequency of window cleaning to maintain clear views.</li>
<li>Ensure that all spills and spots are promptly cleaned from tables and other
surfaces.</li>
</ol>



    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        Based on the provided snippets, guests have expressed satisfaction with the hotel's
cleanliness and comfort. The reviews highlight the hotel's well-equipped rooms,
which include kitchen appliances, washing machines, and spacious layouts. The
cleanliness is consistently praised, with specific mentions of daily housekeeping,
spotless rooms, and attention to detail. The hotel's amenities, such as the pool and
gym, though sometimes noted as small, are appreciated for their maintenance and
accessibility. The comfort of the beds and the quality of the linen also receive
positive feedback, contributing to a comfortable stay for guests. Overall, the hotel
seems to be doing well in providing a clean and comfortable environment for its
guests 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

<li>Spacious and well-equipped rooms (e.g., &quot;big space,&quot; &quot;room is well equipped
with almost everything you need,&quot; &quot;rooms have high ceilings and are
spacious&quot;)</li>
<li>Cleanliness maintained throughout the hotel (e.g., &quot;absolutely clean,&quot; &quot;all
areas are very clean,&quot; &quot;cleaning service is perfect&quot;)</li>
<li>Quality and comfort of beds and linen (e.g., &quot;bed is very large,&quot; &quot;bed sheets
are changed daily,&quot; &quot;beds were clean&quot;)</li>
<li>Well-maintained amenities (e.g., &quot;amenities and hotel facilities are well-kept,&quot;
&quot;excellent swimming pool,&quot; &quot;gym was small but also as expected&quot;)</li>
<li>Efficient housekeeping service (e.g., &quot;cleaning was exception every day,&quot;
&quot;cleaned our room daily,&quot; &quot;housekeeping was very good and clean&quot;)</li>
<li>Additional conveniences (e.g., &quot;washer/dryer was an added bonus,&quot; &quot;well
equipped kitchen,&quot; &quot;access to private swimming pool was appreciated&quot;)</li>

    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

<li>Addressing any feedback regarding the size of the gym and pool to see if
expansions or improvements are feasible.</li>
<li>Continuing to ensure that all amenities are accessible and well-maintained at
all times.</li>
<li>Maintaining the high standards of cleanliness and comfort that guests have
come to expect, as these are clearly areas where the hotel excels.</li>

    </ol> 
    </Tab>
</Tabs>