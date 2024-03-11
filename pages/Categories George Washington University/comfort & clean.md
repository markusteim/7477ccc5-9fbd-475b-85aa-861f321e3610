# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary 

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 



In the snippets provided, the sentiment regarding comfort and cleanliness at the university is a mixed bag, with a notable number of both positive and negative experiences. Positive remarks often highlight spacious and well-maintained housing options, while negative comments frequently point out issues with weather unpredictability, inadequate facilities, and cleanliness concerns.


```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN TRIM(LOWER(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN TRIM(LOWER(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT Snippet) AS category_count
    FROM
        hotels.titles
    WHERE date >= '2009-01-01' AND date <= '2023-12-31'
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
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
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
    echartsOptions={{
    xAxis: {
      axisLabel: {
        rotate: 45 // Rotate labels by 45 degrees if on mobile
      }
    }
  }}
/>


## Positive:

- **Housing Quality:** Students appreciate the spaciousness and cleanliness of the dorms, with private bathrooms and in-room amenities like washers, dryers, and kitchen facilities being particularly praised.
- **Temperature Control:** The availability of good air conditioning and heating in the dorms is a highlight, ensuring comfort across seasons.
- **Clean Environment:** Cleanliness in certain housing facilities and the reliability of services like the Vex shuttle are noted as positives.
- **Facility Maintenance:** Some facilities are described as well-maintained, with modern computers and well-equipped rooms.
- **Housing Options:** There is a variety of housing options available, including off-campus accommodations that are spacious and well-regarded.


## Negative:

- **Facility Size:** Students find some facilities, like the gym and study rooms, to be too small, crowded, and sometimes in disrepair.
- **Cleanliness Issues:** There are frequent complaints about the cleanliness of certain dorms, with reports of mold, filth, and unpleasant odors.
- **Weather Challenges:** The unpredictable and sometimes extreme weather in D.C. is a common grievance, affecting comfort and convenience.
- **Housing Disparities:** Freshman housing receives criticism for being old, cramped, and sometimes lacking in cleanliness and amenities.
- **Temperature Extremes:** Complaints about the weather include it being too hot, humid, or cold, which impacts the overall comfort of students.



<br>

## Most Positive Examples:
- "housing is great"
- "spacious rooms"
- "private bath"
- "much cleaner"
- "dorms are spacious"


 

## Most Negative Examples:
- "athletic facilities available to students for everyday use aren't large enough"
- "most of them are disgusting and in disrepair"
- "Thurston is disgusting most of the time"
- "gym is awful"
- "rooms are tiny"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
    COUNT(DISTINCT Snippet) AS ReviewCount, -- Count unique review IDs
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
        
The student reviews indicate a range of concerns primarily focused on the quality and condition of the dormitories and the impact of weather on student comfort. The dorms are described as varying widely in quality, with some being modern and others in disrepair. Issues such as overcrowding, cleanliness, and maintenance problems like mold and pests are mentioned. The weather also seems to affect student comfort, with complaints about the heat, humidity, and cold, as well as the inconvenience of rain and unpredictable weather patterns. The reviews suggest that the university's housing and facilities may not be meeting the expectations of the students in terms of comfort and cleanliness.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Inconsistent quality of dormitories (housing varies depending on dorm placement, dorms can be disappointing, dorm options range from modern and updated to horrid).
- Overcrowding and space issues (bit cramped sometimes, they stuff as many students as they can into one room, student housing is a little cramped on space).
- Maintenance and cleanliness concerns (could be cleaner, room was not clean and it was falling apart, mold, cockroaches, rooms are dusty and moldy, filled with mold, just smell horrible, gross dorms).
- Specific dormitory complaints (dorms are nice except for Thurston, Thurston is disgusting most of the time, every other dorm I've lived in was old and disgusting, Mitchell Hall which has communal bathrooms).
- Athletic and study facilities not meeting needs (locker rooms are too small, athletic facilities available to students for everyday use aren't large enough, study areas could be improved).
- Weather-related discomfort (extremely hot and humid in the summer, cold and dry in the winter, weather is bipolar, walking in rain or snow is invisible, weather in D.C. is pretty terrible).
- General dissatisfaction with freshman housing (freshman housing options were not great, most of the housing for freshmen is in Thurston Hall, which is disgusting, freshman dorms aren't something to rant about).


    </ul>

<br>

<b>Suggestions:</b>
<br>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Conduct a thorough review and renovation of dormitories, prioritizing those with the most negative feedback.
- Explore options to alleviate overcrowding, such as building new housing or reconfiguring existing spaces.
- Implement a regular maintenance and cleaning schedule to address issues of mold, pests, and general cleanliness.
- Consider upgrading athletic and study facilities to accommodate the student population adequately.
- Provide more covered walkways or shuttle services to help students navigate inclement weather.
- Improve communication about housing options and processes to manage expectations and reduce uncertainty for incoming students.

</ul>
    
</Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university's housing facilities receive positive feedback from students, highlighting the comfort and cleanliness of the dormitories and on-campus living options. Students appreciate the availability of private rooms, private bathrooms, and in-room amenities such as microwaves, fridges, and freezers. The progression of housing quality with each academic year is noted, with upperclassmen enjoying more spacious and well-equipped accommodations. The presence of kitchens, laundry facilities, and study areas within the dorms contributes to a convenient living experience. Additionally, the university's location in D.C. is mentioned favorably for its mild winters and moderate weather, which seems to enhance the overall campus experience.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Private rooms ("having a private room is the best")
- Private bathrooms in all dorms ("all dorms have private bathrooms")
- Variety of housing options ("plenty of room", "a lot of different housing options")
- In-room amenities (e.g., "your own microwave/fridge/freezer in every room")
- Quality of facilities ("facilities alike are top tier", "facilities are pretty good")
- Cleanliness and maintenance ("dorms are very well maintained", "libraries and study rooms are always kept clean")
- Weather and climate appreciation ("weather in d.c. changes frequently", "it rarely gets extremely cold")
- Spacious accommodations ("west hall dorms are spacious", "rooms themselves are large")
- Availability of kitchens and laundry facilities ("dinning hall plus kitchen and laundry on every floor", "each room has its own washer and dryer")
- Mild and enjoyable seasons ("four seasons", "spring and fall are very mild and enjoyable")


</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Continue maintaining and upgrading older dorm facilities to match the standards of newer ones.
- Ensure consistent quality of amenities across all housing options.
- Explore additional on-campus housing opportunities to guarantee housing for all four years, if feasible.
- Maintain the cleanliness and functionality of shared spaces like kitchens, laundry rooms, and study areas.
- Keep up the good work with the weather preparedness, ensuring sidewalks and paths are safe during adverse weather conditions.

</ul> 
    </Tab>
</Tabs>