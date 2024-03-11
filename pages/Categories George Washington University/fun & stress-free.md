# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*


 
# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the university reviews regarding fun and vibrant experiences leans more towards the positive, with numerous mentions of the exciting city life, proximity to landmarks, and a variety of social activities. However, there is a clear indication of some negative aspects, particularly related to the limitations in dining options and the challenges of campus parties.


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
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2020-01-01' AND '2023-12-31'
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
        "#85144B", // A shade of dark red
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


## Positive:
- **City Life:** Students revel in the university's prime location in the heart of D.C., with easy access to the White House, national monuments, and a plethora of cultural events.
- **Social Scene:** Greek life dominates the social landscape, offering numerous parties and events, while the city's nightlife provides a plethora of bars and clubs for those seeking a vibrant scene.
- **Athletics Fun:** Varsity basketball garners significant attention, with games being a focal point for school spirit and social gatherings.
- **Leisurely Learning:** Some programs allow for a leisurely academic pace, and the combination of live and pre-recorded lectures caters to diverse learning preferences.
- **Campus Activities:** Despite some criticism, there are mentions of fun case studies, guest speakers, and a bustling atmosphere on campus that contribute to a lively student experience.
 

## Negative:
- **Dining Dilemmas:** Students face challenges with limited dining hall hours, especially during finals week, and the inconvenience of the main dining area being closed on weekends.
- **Party Problems:** Campus parties often receive criticism for being subpar, with issues like overcrowding, strict entry policies, and a lack of vibrancy compared to state schools.
- **Community Concerns:** A recurring theme is the lack of a strong sense of community, with students feeling the need to actively seek out social connections.
- **Event Shortages:** There are grievances about the university not hosting enough events to foster a carefree and inclusive environment for all students.
- **Athletic Apathy:** Aside from basketball, sports do not seem to play a significant role in campus life, with low student turnout at games and minimal fanfare.


<br>

## Most Positive Examples:
- "right in the city"
- "varsity basketball is incredible"
- "never bored"
- "socially fulfilled"
- "i have fallen in love with the location"



## Most Negative Examples:
- "which isn't great considering students need to eat well before finals"
- "only one dining hall was open"
- "they didn't host events"
- "almost nobody goes out of a friday which is really weird"
- "extremely difficult to be a scholarship/fws student here"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
        
Based on the student reviews, the university appears to face challenges in fostering a sense of community and school spirit, as well as managing the logistics and support for both on-campus and virtual learning environments. Students express concerns over the lack of athletic focus, limited social activities, and the need for greater academic support. The virtual learning experience has been notably difficult, with issues such as poor internet connectivity and non-interactive classes. Additionally, the high cost of attendance and living, coupled with expensive dining options, adds financial stress. The reviews also indicate a need for better event planning, more accessible student services, and improved communication from the administration.


        <br>
        <br>

    <b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Difficulty with virtual learning and lack of interactive classes (virtual learning has been difficult, zoom classes were not very interactive).
- Lack of athletic focus and school spirit (do not come here if you care about athletics, there is no school spirit).
- High cost of attendance and living expenses (expensive though, drinks are expensive).
- Poor internet connectivity (many times there would be an issue with the connection).
- Limited dining options and inconvenient hours (only one dining hall was open, dining halls during finals week).
- Need for more academic support (more support in those challenging courses would be helpful).
- Lack of social activities and community feeling (not really a party school, there is not much to do there/around that campus).
- Housing issues, including limited availability and high costs (limited sophomore housing, many people prefer to live off campus).
- Administrative challenges, such as registration difficulties and staff turnover (problema is registration, people were constantly leaving in administration).
- Inadequate communication and event planning (they didn't host events, it tends to become a hassle to seek academic guidance).


</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Enhance the virtual learning platform to increase interactivity and reliability.
- Develop programs to boost school spirit and athletic engagement.
- Review and adjust the cost structure to alleviate financial burdens on students.
- Upgrade internet infrastructure to ensure stable and fast connectivity.
- Expand dining options and adjust hours to meet student needs, especially during finals.
- Provide additional academic support services, such as tutoring and advising.
- Increase the number and variety of social events to foster community.
- Reevaluate housing policies to improve availability and affordability.
- Streamline administrative processes and improve communication with students.
- Implement a more robust event planning and management system.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university appears to provide a comprehensive and engaging educational experience, with a strong emphasis on leveraging its location in Washington D.C. to enhance student life and learning. Students appreciate the vibrant city environment, the proximity to significant landmarks and institutions, and the wealth of opportunities for internships and cultural experiences. The campus offers a variety of extracurricular activities, sports, and social events, with an active Greek life and a sports scene centered around basketball. Academically, the university is described as rigorous, with flexible scheduling and a diverse range of courses that cater to students' interests and career aspirations. The integration of technology in learning, through platforms like Blackboard and Zoom, is also noted as a positive aspect, especially during periods of remote learning. Overall, students seem to enjoy a balance of challenging academics and a rich social life, facilitated by the university's urban setting and resources.


        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Vibrant city environment and proximity to landmarks (e.g., "living in the heart of D.C.", "near the White House", "monuments a 15-minute walk away").
- Diverse opportunities for internships and cultural experiences (e.g., "city offers many job opportunities", "internship availabilities").
- Active student life with numerous extracurricular activities (e.g., "many extracurriculars", "tons of student organizations").
- Engaging sports scene, particularly basketball (e.g., "basketball is the sport of this school", "people attend basketball games").
- Rigorous and flexible academic programs (e.g., "very rigorous academics", "flexible schedule", "classes exactly what I needed for my major").
- Effective use of technology in education (e.g., "Blackboard is easy to use", "professors find ways to challenge you through the technology platform").
- Supportive campus resources (e.g., "resources such as career help, pre-law, tuition, essay help, and volunteering, are within reach").
- Accessible and convenient campus location (e.g., "main campus is conveniently located near a metro stop", "foggy bottom metro near by").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could consider providing students with a metro card similar to American University's to facilitate exploration and travel for work and internships. 
- Additionally, expanding the variety and frequency of campus events and activities could further enrich the student community. 
- It may also be beneficial to continue developing the integration of technology in learning, ensuring that all students have access to the necessary resources and support for their academic pursuits.

</ul> 
    </Tab>
</Tabs>