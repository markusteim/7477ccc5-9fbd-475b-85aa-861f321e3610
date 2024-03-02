

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
    AND TRIM(Category) = 'well-being'
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
    colorPalette={
        [
        "#FF4136", // A shade of red
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>


<br>


## Positive:

1. Gym Facilities: Guests praised the 24/7 gym, highlighting the variety of cardio equipment and weights,
and the inclusion of a sauna.
2. Relaxation Spots: The infinity pool and sun deck were noted as havens for relaxation, with comfortable
poolside beds readily available.
3. Wellness Options: The availability of a healthy food section at breakfast and the presence of a spa
center contributed to guests' well-being.
4. Comfortable Stays: Many found their rooms and the hotel environment comfortable and relaxing,
likening it to a home away from home.
5. Location Benefits: The hotel's location facilitated relaxing walks to the marina, harbour, and beach,
enhancing guests' overall well-being.
 

## Negative:

1. Limited Relaxation: Some guests found no comfortable common area to relax in, affecting their ability
to unwind.
2. Environmental Issues: A few guests mentioned sunburn risks and sleep disturbances due to traffic
noise.
3. Health Concerns: There were instances of guests falling ill or requiring medical assistance during their
stay.
4. Gym Space: A minority of guests felt the gym was a bit small and lacked sufficient equipment for their
workouts

<br>

## Most Positive Examples:

1. "best-class gym facility with studio"
2. "healthy food section is offered at the breakfast"
3. "infinity pool and sun deck provides a haven for relaxation"
4. "environment set of the restaurant is very very calm & relaxing"
5. "we felt confortable all the time"

 

## Most Negative Examples:

1. "no comfortable common area to relax in"
2. "sleep disturbed by all the traffic"
3. "fell ill during my stay"
4. "gym equipment and space is not enough"
5. "could not lay and relax by pool and could hear from room"

<br>





# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
    AND TRIM(LOWER(Category)) = 'well-being' -- Change category as needed
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
        
        The guest reviews suggest that the hotel has several areas of concern related to the
physical and mental well-being and wellness of its patrons. Issues range from
inadequate sun protection advice, noise disturbances, and insufficient gym facilities
to concerns about the availability of medical assistance and dietary
accommodations. While the reviews do not indicate systemic failures, they do
highlight specific aspects that could be improved to enhance the overall guest
experience in terms of comfort, relaxation, and health support. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Inadequate sun protection advice (&quot;be careful though as you need to plaster
on the sun lotion or you’ll get roasted!&quot;)</li>
<li>Noise disturbances by the pool and from traffic (&quot;could not lay and relax by
pool and could hear from room&quot;, &quot;sleep disturbed by all the traffic&quot;)</li>
<li>Distance from downtown Dubai (&quot;downtown dubai is a bit of a trek&quot;)</li>
<li>Guests falling ill (&quot;fell ill during my stay&quot;, &quot;half of us were sick when we
arrived&quot;, &quot;required medical assistance&quot;)</li>
<li>Insufficient gym facilities (&quot;gym equipment and space is not enough for the
normal exercise or training of a person&quot;, &quot;gym is a bit small&quot;)</li>
<li>Sauna facilities need improvement (&quot;just need to improve sauna facilities a
bit&quot;)</li>
<li>Lack of immediate dietary accommodation for a diabetic guest (&quot;my wife is
diabetic was already experiencing hypoglycemia, that she eventually
approached another staff just to have some bread to eat since the staff didn’t
get back to us&quot;)</li>
<li>No comfortable common area to relax in (&quot;no comfortable common area to
relax in&quot;)</li>
<li>General lack of a relaxing environment (&quot;not very relaxing&quot;)</li>
<li>Sleep disturbances (&quot;we couldn&#39;t even sleep&quot;</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To address these issues, the hotel could implement the following
improvements:</li>
<li>Provide guests with clear information on sun safety and complimentary
sunscreen at poolside areas.</li>
<li>Enhance soundproofing in rooms and around the pool area to minimize noise
disturbances.</li>
<li>Consider offering a shuttle service to downtown Dubai to make the location
more convenient for guests.</li>
<li>Review and improve protocols for handling guest illnesses, including prompt
medical assistance.</li>
<li>Expand and upgrade the gym facilities to accommodate more guests and a
wider range of exercises.</li>
<li>Upgrade the sauna facilities to meet guest expectations.</li>
<li>Train staff to respond more quickly to dietary needs, especially for guests with
medical conditions like diabetes.</li>
<li>Create a designated quiet common area with comfortable seating for guests
to relax in.
<li>Investigate and address the causes of sleep disturbances, such as traffic
noise, to ensure a more restful environment for guests</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        Based on the guest reviews, the hotel seems to excel in providing facilities and
services that contribute to the physical and mental well-being of its guests. The gym
is frequently mentioned as a standout feature, with its extensive equipment and 24/7
availability. Guests appreciate the comfortable accommodations, including the quality
of sleep provided by the bed mattress and duvet. The hotel's location is also noted
for its convenience and the ability to relax, with easy access to the marina, harbour
areas, and beach. The pool area is praised for its comfort and availability of
sunbeds, while the sauna and spa services add to the relaxation experience. The
hotel's environment, including the restaurant setting, is described as calm and
relaxing, which seems to enhance the overall guest experience. 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Gym facilities (e.g., &quot;amazing gym,&quot; &quot;gym works 24/7,&quot; &quot;best-class gym facility
with studio&quot;)</li>
<li>Comfortable accommodations (e.g., &quot;bed mattress and duvet guides good
sleep,&quot; &quot;felt so comfortable and satisfied staying at this hotel&quot;)</li>
<li>Relaxation opportunities (e.g., &quot;infinity pool and sun deck provides a haven for
relaxation,&quot; &quot;spa was amazing&quot;)</li>
<li>Location and accessibility (e.g., &quot;comfortable 20-25 minute walk to the marina
and harbour areas,&quot; &quot;easy to get beds by the poolside&quot;)</li>
<li>Calm and relaxing environment (e.g., &quot;environment set of the restaurant is
very very calm &amp; relaxing,&quot; &quot;place was so calm and comfortable&quot;)</li>
<li>Additional wellness amenities (e.g., &quot;healthy food section is offered at the
breakfast,&quot; &quot;sauna is small yet subtle and effective&quot;)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To further enhance the guest experience, the hotel could consider expanding
the relaxation and wellness options.</li>
<li>This could include offering wellness workshops, yoga classes, or guided
meditation sessions. Additionally, ensuring that the spa and sauna facilities
are well-maintained and promoting their availability could attract guests
looking for a comprehensive wellness experience.</li>
<li>It may also be beneficial to provide more information about local walking
routes or outdoor activities that promote well-being.</li>
<li>Lastly, considering guest feedback for continuous improvement of the gym
equipment and facilities would ensure the hotel remains a top choice for
health-conscious travelers.</li>


    </ol> 
    </Tab>
</Tabs>