# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The reviews present a mixed sentiment regarding physical and mental well-being at the university. Positive comments often highlight the convenience and quality of transportation, fitness facilities, and the ease of accessing health services. Conversely, negative remarks touch on the challenges of maintaining a healthy lifestyle, the lack of green spaces, and the inefficiency of certain health services.


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
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'well-being'
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


<br>


## Positive:
- **Campus Mobility:** Students appreciate the ability to walk everywhere, with frequent shuttles and public transportation easing commutes.
- **Health Facilities:** The presence of multiple gyms, including a superb main campus gym, and efficient medical emergency services are praised.
- **Recreational Options:** Access to sports fields, pools, and LSPA courses for trying out sports activities is seen as a positive aspect.
- **Healthcare Access:** The university's efficiency in providing required vaccines and the availability of medical assistance are commended.
- **Transportation Abundance:** The convenience of the metro, buses, and other transportation options, including a shuttle that runs often, is highlighted as a positive feature.

 

## Negative:
- **Dietary Struggles:** Students struggle to maintain a good diet, with some expressing dissatisfaction with the health services and the availability of healthy food options.
- **Stressful Environment:** The university's environment is described as stressful, potentially impacting mental health.
- **Accessibility Issues:** Some students find the distance to certain facilities, like the health center or their classes, inconvenient.
- **Health Services Critique:** There are complaints about the organization and effectiveness of student health services, with some students feeling their needs are not adequately met.
- **Limited Exercise Facilities:** Non-varsity athletes express frustration over restricted access to certain athletic facilities.

<br>

## Most Positive Examples:
- "I love being able to walk everywhere."
- "Gym is nice."
- "Classes are close enough."
- "Transportation is excellent here."
- "I have never heard of a student being hurt by anyone or anything."


 

## Most Negative Examples:
- "Just don't vomit everywhere."
- "Student health services makes you want to suffer through the disease instead of going to their office."
- "Mental health services are impossible to get in contact with."
- "Student health services does not seem to be organized."
- "Mental health resources are sorely lacking."

<br>





# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
        
The university appears to be facing several challenges related to the physical and mental well-being of its students. Issues range from inadequate health services to transportation difficulties and lack of healthy food options. Students express frustration with the accessibility and quality of mental health services, the physical distance of health facilities from campus, and the reliability of campus shuttles. Additionally, concerns about the environment, such as crowdedness, lack of green spaces, and safety during inclement weather, contribute to the overall stress. The gym and main library facilities also receive criticism, indicating a need for improvement in recreational and study spaces.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Inconsistent shuttle service ("vex shuttle service is spotty at best").
- Excessive homework load ("had to do close to 30 hours of homework every week").
- Difficulty accessing mental health services ("mental health services are impossible to get in contact with").
- Busy and crowded streets with no scenery, affecting the campus environment ("very busy streets", "crowded", "no scenery").
- Privacy concerns and issues with financial aid, Title IX, and mental health opportunities ("privacy", "financial aid, title ix issues, and mental health opportunities").
- Limited access to health and wellness facilities ("learners health and wellness center (aka hellwell) is where the rest of us go", "unless you are varsity, you can't use them").
- Lack of mental health resources ("mental health resources are sorely lacking").
- Inconvenient location of health services ("trek up to foxhall rd get real old real fast", "student health is a 10-15 minute walk from campus", "student health services is at least a 20 minute walk from the center of campus").
- Poor food options for health and late-night studying ("had lead to many students struggling to maintain a good diet", "very difficult as a student when studying for tests and finals to find any healthy late-night energy in food").
- Inadequate health care at the university hospital ("gw hospital is not somewhere i'd suggest going", "health is not great").
- Parking difficulties ("parking's a hassle").
- Misdiagnosis and incorrect treatment at health services ("they have not once diagnosed me with the correct problem", "let alone gave me the correct treatment").
- Insufficient equipment and facilities at the health and wellness center ("student health and wellness center is lacking the equipment and facilities necessary for a school so big").
- Elevator issues in campus buildings ("elevator took at least 15 minutes to go from the 4th floor to the lobby", "madison hall elevator is the worst elevator on campus").
- Stressful campus environment ("stressful environment").
- Disorganization of student health services ("student health services does not seem to be organized").
- Distance of housing from academic buildings ("only downfall to living on e street is that it is far from most of my classes").
- Focus of health services on acute alcohol-related issues ("they're mostly concerned with kids with alcohol poisoning").
- Limited public transportation options ("public transportation is well, public transportation", "metro is pretty small", "doesn’t go many places").
- Lack of green spaces and emphasis on sports ("not many green areas", "sports is not a big deal here").
- Safety concerns during adverse weather conditions ("when it snows and is iced, we should not have to walk all over the campus to go to class when it is dangerous").
- Negative perception of student health services ("student health services makes you want to suffer through the disease instead of going to their office").
- Limited parking for students ("there is not a lot of student parking").
- Poor quality of gym and library facilities ("gym is really bad too", "main library is awful").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Enhance the reliability and coverage of the shuttle service.
- Review and potentially adjust the academic workload to ensure a healthy work-life balance for students.
- Increase the accessibility and availability of mental health services, possibly through online platforms or extended hours.
- Improve the campus environment by adding green spaces and ensuring safe walkways during inclement weather.
- Address privacy and financial aid concerns through transparent policies and support systems.
- Expand health and wellness facilities to accommodate non-varsity students and increase equipment and staff.
- Offer healthier food options, especially for late-night studying.
- Improve the quality of care at the university hospital and student health services, including accurate diagnoses and treatments.
- Address parking issues by increasing student parking spaces or promoting alternative transportation.
- Upgrade the gym and library facilities to meet the needs of the student population.
- Ensure that student health services are well-organized and staffed with qualified health professionals.
- Consider the location of student housing in relation to academic buildings to minimize travel time.
- Broaden the scope of health services to address a wider range of health concerns beyond acute alcohol-related issues.
- Enhance public transportation options to better serve the student population.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the university offers a variety of facilities and services that contribute to the physical and mental well-being of its students. The presence of multiple gyms with comprehensive equipment, including an indoor track, swimming pool, squash courts, basketball courts, and boxing equipment, is frequently mentioned. The convenience of campus layout, with classes and facilities within walking distance, is appreciated. The university's integration with public transportation, including a metro stop on campus, shuttles, and the availability of bikes and Zipcars, facilitates easy movement around and beyond the campus. The Mount Vernon campus is noted for its athletic fields, and the Lerner Health and Wellness Center is highlighted for its gym facilities. The university's academic environment is described as collaborative and conducive to personal growth, with additional resources such as asynchronous material for study support. The health center is commended for its services, and the availability of healthy food options is also noted.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Comprehensive gym facilities (indoor track, swimming pool, squash courts, basketball courts, boxing equipment)
- Convenient campus layout and proximity of classes
- Public transportation options (metro stop, shuttles, bikes, Zipcars)
- Athletic fields on the Mount Vernon campus
- Lerner Health and Wellness Center gym
- Collaborative academic environment
- Study support resources (asynchronous material)
- Health center services and availability of healthy food options
- Safe campus environment with good security measures

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance student well-being, the university could consider extending gym hours to accommodate different student schedules, increasing the variety of healthy food options, and possibly offering more wellness programs or workshops focused on stress management and mental health. 
- Additionally, ensuring that all facilities are well-maintained and that transportation services remain reliable will continue to support the positive experiences of students.

</ul> 
    </Tab>
</Tabs>