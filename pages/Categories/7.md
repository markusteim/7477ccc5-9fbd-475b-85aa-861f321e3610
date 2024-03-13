# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


In the snippets provided, the overall sentiment leans heavily towards the positive, with students expressing a strong sense of pride, self-worth, and confidence stemming from their association with the university. The positive experiences often highlight the university's prestigious reputation, successful alumni network, and the personal growth opportunities available. In contrast, the negative sentiments are fewer and tend to focus on social dynamics and personal challenges faced by the students.


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
    AND TRIM(Category) = 'self-worth, pride & self-awareness'
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
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'self-worth, pride & self-awareness'
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
- **Prestigious Reputation:** Students express immense pride in attending a university that is highly ranked (#20 in the nation) and holds a great reputation, enhancing their self-worth.
- **Alumni Network:** The incredible alumni network boosts students' confidence, providing them with a sense of belonging to a community of successful individuals.
- **Academic Excellence:** The university's exceptional academic programs, such as the Elliott School of International Affairs, instill a sense of achievement and self-confidence in students.
- **Athletic Pride:** Participation in sports teams that perform well at national levels contributes to a strong sense of pride and community among students.
- **Scholarship Recognition:** Receiving scholarships, such as the $3,000 award from the Corcoran, validates students' past achievements and boosts their self-esteem.


## Negative:
- **Social Discontent:** Some students feel a negative impact on their self-worth due to peers who are perceived as "stuck up and snotty" or who "believe they are better than everyone else."
- **Academic Pressure:** The reality of students dropping out or failing classes can undermine self-confidence and contribute to a sense of inadequacy.


<br>

## Most Positive Examples:
- "gw really is an excellent school"
- "i've never felt more lucky"
- "i believe i am set up for future success as a gw student"
- "i am so happy to have been accepted at george washington university"
- "i got the presidential scholarship"



## Most Negative Examples:
- "people here are extremely stuck up and snotty"
- "people drop out, fail out of classes"
- "but i need the money"
- "believe they are better than every one else"
- "people are generally not proud of the fact that they go to gw"

<br>





<br>

## Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
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
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
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
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
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
    AND TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness' -- Change category as needed
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
        
The snippets provided suggest that there are some areas of concern regarding the university's environment and its impact on students' sense of self-worth and pride. The issues range from academic challenges, such as students dropping out or failing classes, to social dynamics, including perceptions of elitism and a lack of pride in the university community. These snippets indicate that some students may be experiencing a negative atmosphere that could affect their self-confidence and self-awareness.


        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Academic difficulties leading to dropout or failure (people drop out, fail out of classes).
- Financial pressures affecting students (but I need the money).
- Social elitism and superiority complex among students (believe they are better than everyone else).
- Lack of pride in the university affiliation (people are generally not proud of the fact that they go to gw).
- Mediocre experiences or offerings (most of which are mediocre).
- Unfriendly and pretentious student body (people here are extremely stuck up and snotty).


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these issues, the university could consider implementing a more robust academic support system to help students who are struggling with their courses. This could include tutoring services, study groups, and workshops on study skills and time management. Additionally, financial counseling and support services could be expanded to assist students who are facing economic challenges.

- To combat the perception of elitism and improve the sense of community, the university could initiate programs that foster inclusivity and celebrate diversity. This might involve peer mentorship programs, community-building events, and workshops on social awareness and empathy.

- To enhance pride in the university, efforts could be made to highlight the achievements and positive aspects of the institution through marketing campaigns, alumni success stories, and showcasing student accomplishments. Finally, addressing the unfriendly social atmosphere may require a review of the campus culture and the introduction of initiatives aimed at promoting kindness, respect, and collegiality among students.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university appears to be fostering an environment where students feel a strong sense of achievement and pride, particularly in relation to their academic and professional development. The presence of successful alumni and the university's prestige in specific fields, such as international affairs and political science, contribute to this sentiment. Students express gratitude for the opportunities provided by the university, including scholarships and the ability to grow personally and professionally. The location in Washington D.C. is also seen as a significant advantage, offering unique opportunities for networking and attending high-profile events. The sports teams' performance and the recognition of women's sports add to the sense of pride within the student body.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Academic and professional growth ("proving to myself that I am capable of doing great things", "i believe i am set up for future success as a gw student")
- Prestigious reputation ("they've done a good job of enhancing our prestige", "gets a degree from a prestigious private school in Washington D.C.")
- Strong programs and rankings ("elliot is number 7 in the nation for best international affairs school", "the best place to be for political science")
- Successful alumni ("has some successful alumni")
- Scholarships and financial support ("received an awesome merit scholarship", "i got a scholarship from the corcoran of 3,000.00 dollars")
- Sports achievements and recognition ("especially for women’s sports, it’s really good", "basketball team has gotten great recognition and is doing well")
- Networking opportunities ("networking is the key to success in d.c")
- Location benefits ("when the imf and the world bank hold conferences in your auditorium", "international companies, major law firms, and nonprofit organizations are headquartered in d.c")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could focus on expanding scholarship opportunities, especially for low-income students, to ensure a diverse and inclusive environment. 
- Additionally, continuing to support and promote the achievements of sports teams and individual athletes can strengthen school spirit. 
- The university might also consider increasing efforts to facilitate networking opportunities for students across all disciplines, not just those in high-profile fields. 
- Lastly, maintaining and improving the quality of academic programs through regular reviews and updates will help sustain the university's reputation and student satisfaction.

</ul> 
    </Tab>
</Tabs>