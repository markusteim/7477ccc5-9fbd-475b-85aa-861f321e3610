# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The general sentiment from the snippets provided shows a mix of enthusiasm and criticism towards the university experience. Positive remarks often highlight the vibrant campus life and the unique opportunities available due to the university's location. In contrast, negative comments tend to focus on specific aspects that fall short of expectations, such as housing and dining experiences. The positive comments seem to outnumber the negative ones, suggesting a generally favorable view of the university among students.

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
    AND TRIM(Category) = 'impressions'
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
    AND LOWER(TRIM(Category)) = 'impressions'
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
- **Campus Life:**  Students express a strong affection for the university, with phrases like "absolutely love gw" and "great experience this far" indicating a deep sense of satisfaction and happiness with their campus life.
- **Academic Opportunities:**  The proximity to D.C. is frequently mentioned as a significant advantage, offering unique academic and professional opportunities, as seen in comments like "perfect match" and "not sure if there's a better school for interning."
- **Social Environment:**  The vibrant social scene is a highlight for many, with students enjoying the diversity and the chance to engage in various activities, as captured by "hopeing to explore more of what the school has to offer."
- **Recommendations:**  A number of students would readily recommend the university to others, suggesting a strong endorsement of the institution's overall environment and offerings.
- **Personal Growth:**  The university is seen as a place of personal development and joy, with students stating "i have never felt happier" and "i wouldn't change my decision to come to gw."


 

## Negative:* 
- **Housing Concerns:**  Several students express dissatisfaction with their living arrangements, particularly those placed on the Mount Vernon campus, describing it as a "huge hassle" and advising against housing students there.
- **Dining Discontent:**  The dining options receive criticism for being limited and not meeting expectations, with phrases like "j street sucks" and "you get sick of stuff easily" indicating a lack of satisfaction.
- **Social Exclusion:**  Some students feel marginalized if they are not part of specific groups or majors, as seen in comments like "if you're not an international affairs major, you're screwed."
- **Financial Burden:**  The cost of attending the university is a concern for some, with mentions of it being "pricey" and the financial aid process being described as frustrating.
- **Technical Troubles:**  Issues with campus Wi-Fi and the complexity of connecting devices are sources of annoyance, as students mention "a million steps and downloads required just to connect for the first time."

<br>

## Most Positive Examples:
- "absolutely love gw"
- "great experience this far"
- "perfect match"
- "i have never felt happier"
- "i wouldn't change my decision to come to gw"


 

## Most Negative Examples:
- "huge hassle"
- "j street sucks"
- "if you're not an international affairs major, you're screwed"
- "pricey"
- "a million steps and downloads required just to connect for the first time"

<br>





<br>

## Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
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
WHERE TRIM(LOWER(Category)) = 'impressions'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
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
WHERE TRIM(LOWER(Category)) = 'impressions'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
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
    AND TRIM(LOWER(Category)) = 'impressions' -- Change category as needed
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
        
The student reviews indicate a range of challenges faced by attendees at the university. Concerns are expressed about the online learning experience, the adjustment to university life, and the handling of the fall semester. Students find the financial aid process, class registration, and student organization involvement difficult. Housing and dining situations are also criticized, with specific mention of the Mount Vernon campus being less desirable. The reviews suggest a competitive atmosphere, with dissatisfaction in the variety of majors and the support for non-political science or business majors. The admissions process, campus housing, and the cost of living in D.C. are additional points of contention. The sentiment towards Greek life is mixed, and there are complaints about the public transportation system and campus connectivity issues. Overall, the reviews reflect a sense of disappointment and frustration, with several students considering or having already transferred to other institutions.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Online learning experience dissatisfaction ("as a freshman, i wish i did not spend the money to attend gw online", "due to the online classes").
- Adjustment to university life and handling of the semester ("was a difficult adjustment", "fall semester seems to be looking like a disaster").
- Difficulty with financial aid, class registration, and student organizations ("however, i would like to point out that they are very difficult to deal with", "from financial aid to registering for classes to student organizations").
- Limited housing and dining options ("not a lot of options", "avoid the mount vernon campus", "dining and housing situations").
- Competitive and overwhelming environment ("competition is tight", "at times can feel overwhelming").
- Discontent with the variety of majors and academic support ("if you're going to do anything else, i would look elsewhere", "biology is not really an important major here").
- Negative experiences with admissions and campus housing ("terrible experience with admissions process", "campus housing completely depends on what building you live in").
- High cost of living in D.C. and associated financial strain ("while d.c. is extremely expensive", "it is expensive though").
- Mixed feelings about Greek life and its impact on campus life ("personally i do not think greek life is beneficial for campus /student life", "being in some sort of greek like is kind of necessary").
- Public transportation and connectivity issues ("line has taken 45+ minutes to ride", "you also can’t connect a wii or xbox to the wifi").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Enhance the online learning platform and provide better support for students to ensure a smoother transition to university life.
- Streamline processes for financial aid, class registration, and involvement in student organizations to make them more user-friendly.
- Expand housing and dining options, particularly improving the conditions at the Mount Vernon campus.
- Foster a more collaborative rather than competitive atmosphere, and provide more support for a wider range of majors.
- Review and improve the admissions process to make it more transparent and less stressful for prospective students.
- Address the high cost of living by providing more affordable housing options and financial support for students.
- Evaluate the Greek life system and its impact on campus culture, ensuring it is inclusive and beneficial to student life.
- Improve the public transportation system and campus connectivity to facilitate easier movement and access to technology for students.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, students at George Washington University (GWU) appreciate the unique opportunities and experiences the university and its location in Washington D.C. offer. The diverse academic programs, especially in international affairs and political science, are well-regarded. The urban campus environment, proximity to significant landmarks and institutions, and the availability of internships are highlighted as key benefits. The university's response to the pandemic and the shift to online classes were mentioned, indicating adaptability. The sense of community, even for those not involved in Greek life, and the satisfaction with housing options as students progress through their years are also noted. The overall sentiment suggests that students value their time at GWU and the distinct advantages it provides.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Unique opportunities in Washington D.C. ("we are in d.c", "studying international affairs at the elliot school", "gwu provides a unique undergraduate experience in the heart of washington d.c")
- Diverse and strong academic programs ("especially for polisci majors and int'l affairs majors", "as a graduate of the elliott school at gwu, i highly value my experience")
- Urban campus environment and proximity to landmarks ("cherry blossom festival", "white house christmas tree lighting", "smithsonian's")
- Internship opportunities ("being a gw student afforded", "how many interns can say that")
- Adaptability to online classes during the pandemic ("took classes online", "my first year of college online")
- Sense of community and housing satisfaction ("so many people i know enjoyed thurston hall", "most students live off campus", "apartments everywhere")
- Positive experiences with specific schools and programs ("elliott school is great", "law school is pretty good", "i am currently a freshman majoring in biomedical engineering")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could focus on improving the counseling center, as suggested by one snippet, which would contribute to better mental health support. 
- Additionally, continuing to upgrade and maintain technological resources, such as email systems and computer labs, would benefit students. 
- Expanding dining options and ensuring that financial aid processes are transparent and accessible could also improve satisfaction. 
- Lastly, fostering a sense of community for remote and commuting students could enhance their connection to the university.

</ul> 
    </Tab>
</Tabs>