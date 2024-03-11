# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary
Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** of students who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


In the snippets provided, the overall sentiment regarding the sense of belonging and community at the university is predominantly positive. Students often feel supported, included, and cared for by both the faculty and their peers. The university's efforts to maintain connections with alumni, the presence of diverse student organizations, and the accessibility of professors contribute to this positive atmosphere. However, there are instances of negative experiences, particularly related to feelings of isolation and a lack of support from the university staff.

```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN LOWER(TRIM(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN LOWER(TRIM(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN LOWER(TRIM(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT Snippet) AS category_count
    FROM
        hotels.titles
    WHERE date >= '2009-01-01' AND date <= '2023-12-31'
    AND LOWER(TRIM(Category)) = 'belonging & welcomed'
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
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'belonging & welcomed'
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
- **Alumni Engagement:**  Graduates are made to feel special through consistent communication, including emails and magazines, which keep them connected to the university's ongoing activities.
- **Social Integration:**  New students find it easy to make friends, with many organizations and clubs available to support them, creating a welcoming environment from the start.
- **Academic Support:**  Professors and staff are described as knowledgeable, caring, and dedicated to helping students succeed, often going beyond academic support to foster personal connections.
- **Inclusivity and Diversity:**  The campus is noted for its diversity, with students from various backgrounds feeling accepted and supported. Greek life offers additional social opportunities without dominating the campus culture.
- **Financial Aid:**  The university is recognized for ensuring that those in need receive adequate financial aid, demonstrating a commitment to student welfare.


## Negative:
- **Staff Attitude:**  Some students report encounters with unsupportive and unhelpful university staff, which detracts from the sense of community.
- **Greek Life Exclusivity:**  While Greek life provides social opportunities, it can also lead to feelings of isolation and forced conformity for those who choose not to participate or who are not part of it.
- **Indifference to Student Needs:**  There are complaints about the university not caring for its students, with some feeling unfairly treated or that their individual needs are overlooked.
- **Social Division:**  A sense of division is noted, with reports of cliques and a lack of interaction between different student groups, such as athletes and non-athletes or Greeks and non-Greeks.
- **Administrative Challenges:**  Students express frustration with bureaucratic processes and a perceived lack of community, feeling disconnected from the larger student body.


<br>

## Most Positive Examples:

- "once you graduate, as an alumni, they make you feel very special"
- "it is easy to make friends in the first week"
- "professors are very helpful"
- "everyone looks out for each other"
- "this is the first time i truly feel supported"


## Most Negative Examples:

- "staffing is not supportive or helpful"
- "feeling extremely isolated"
- "school does not care about its students at all"
- "there is a long process for rushing/pledging and it's fairly competitive"
- "greeks do have a lot of exclusive events"

<br>



<br>

# Headlines and corresponding snippets from reviews 

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
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
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
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
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
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
    AND TRIM(LOWER(Category)) = 'belonging & welcomed' -- Change category as needed
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
        
The university appears to be facing challenges in fostering a sense of community and belonging among its students. Several students have expressed feelings of isolation and a perception that the university and its staff do not sufficiently care for their well-being. Issues with Greek life exclusivity, ineffective counseling services, and a lack of support from professors and administration are recurrent themes. The campus culture seems to be fragmented, with students feeling disconnected from one another and from the institution itself.


        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Feelings of isolation and lack of community (e.g., "feeling extremely isolated," "does not feel like all students are connected," "just a very isolating place with little support").
- Perceived indifference from staff and professors (e.g., "staffing is not supportive or helpful," "not of them really care about their students," "most profs don't care").
- Greek life exclusivity and its impact on non-Greek students (e.g., "non-greeks feel excluded and 'lesser' than the greeks," "frats and sororities are clique-y and elitist," "if you're not in a fraternity or sorority you miss out on a lot of the social scene").
- Inadequate counseling and support services (e.g., "walk-in counselling is incompetent," "dss at the school does not work with students in a timely manner," "universally regarded health center as having terrible staff").
- Challenges with diversity and inclusivity (e.g., "no sense of belonging here for black kids," "being from a solidly middle-class family, i've found it difficult relating to other students," "there is a perception here that most students come from the northeast and california").
- Administrative and bureaucratic inefficiencies (e.g., "ambigious cyber administration has to approve it," "complicated and mundane process," "charge you over $2,000 for a requirement that has no classroom instruction").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Develop and promote inclusive campus-wide events and programs that encourage interaction among all student groups, including non-Greek and Greek students.
- Enhance counseling and support services by hiring competent staff and providing timely assistance to students in need.
- Implement training programs for professors and staff to improve their approachability and supportiveness towards students.
- Create initiatives that celebrate diversity and foster connections among students from various backgrounds.
- Streamline administrative processes to be more student-friendly and transparent, reducing unnecessary bureaucratic hurdles.
- Establish mentorship programs that pair new students with upperclassmen or alumni to help them navigate university life and build relationships.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the university appears to foster a supportive and inclusive environment that resonates positively with its students. The faculty, including professors and advisors, are frequently described as helpful, caring, and accessible, often going beyond their duties to ensure student success. The university's diverse student body is highlighted as enriching the learning experience, with many students forming lasting friendships and connections. Integration programs for freshmen and ongoing communication with alumni suggest a commitment to student engagement beyond graduation. Student organizations, including Greek life, are noted as avenues for social connection and community building. Overall, the university seems to provide a nurturing and dynamic setting conducive to academic and personal growth.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Supportive faculty (professors really helpful, professors care about you, they love teaching and helping students)
- Strong sense of community (still feel like part of a group, established meaningful friendships, surrounded by a great community)
- Inclusivity and diversity (diverse student body enriches discussions, university is very inclusive, diverse and inclusive community)
- Alumni engagement (alumni community willing to help, send emails and magazines to alumni)
- Student organizations and Greek life (student organization fairs, Greek life is a big part of GW, plenty of student organizations)
- Accessibility and responsiveness (professors available for office hours, staff is helpful and responsive, office was always very responsive)
- Integration and transition programs (freshman integration programs, seamless transition support)
- Personal connections with faculty (professors made sure to extend office hours, one professor had class to her house for dinner)
- Academic support (professors are knowledgeable and willing to take extra time, advisors are very nice and helpful)
- Social environment (friendly people around, easy to make friends, everyone makes many friends there)
- Commitment to student success (they want you to succeed, professors are so understanding, staff here are supportive and helpful)

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could consider expanding mentorship programs to facilitate even more personal and professional development opportunities. 
- Additionally, ensuring that all students, regardless of their involvement in Greek life or other organizations, feel equally included and supported could reinforce the sense of community. 
- Regular feedback sessions with students could help identify areas for improvement and maintain the positive aspects that currently work well.


    </ul> 
    </Tab>
</Tabs>