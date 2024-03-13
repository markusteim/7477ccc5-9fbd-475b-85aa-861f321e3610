# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 

The overall sentiment regarding non-material values and value for money at the university is mixed. Positive comments highlight the professional atmosphere, passionate professors, and the variety of academic options. However, negative remarks point to issues with administration, lack of school spirit, and the high cost of attendance not always reflecting the quality of services provided.

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
    AND TRIM(Category) = 'value & values'
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
    AND LOWER(TRIM(Category)) = 'value & values'
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
- **Professional Atmosphere:** Students appreciate the professional environment, which fosters a great learning opportunity.
- **Internship Opportunities:** The use of platforms like Handshake to connect students with internships at large organizations is well-regarded.
- **Academic Variety:** The wide range of majors and colleges to choose from is a positive aspect for students seeking diverse academic paths.
- **Online Education:** Some students are impressed with the high-quality education delivered in a virtual setting, maintaining academic satisfaction.
- **Passionate Professors:** The passion and engagement of professors are frequently mentioned, contributing to a positive educational experience.

## Negative:
- **High Costs:** The expensive nature of the university is a common concern, with some feeling that the value does not match the price.
- **Inconsistent Quality:** Variations in professor quality and poorly planned courses detract from the academic experience.
- **Lack of School Spirit:** The absence of a traditional college experience and minimal school spirit are noted as negatives.
- **Administration Issues:** Students express frustration with the administration, citing issues such as poor planning and lack of support.
- **Limited Financial Aid:** Concerns about the financial aid process and the high cost of living in D.C. are prevalent among students.


## Most positive examples:
- "very professional atmosphere"
- "great opportunity to learn"
- "amazing professors"
- "great variety of colleges to choose from"
- "professors are well known"



## Most negative examples:
- "school is pricy"
- "entire semester was online"
- "wasn't really planned out well"
- "materials are often 3 or more years old"
- "administration is a big issue"





<br>


# Headlines and corresponding snippets from reviews


```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
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
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
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
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
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
    AND TRIM(LOWER(Category)) = 'value & values' -- Change category as needed
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
        
The university appears to be facing a range of challenges related to financial concerns, student support, and overall satisfaction with the educational experience. Students have expressed dissatisfaction with the cost of tuition, particularly during periods of online learning, and have noted a lack of financial aid and transparency in pricing. There are also concerns about the quality and consistency of education, with some departments being identified as weaker than others. The campus environment is described as lacking cohesion and school spirit, and there are issues with housing affordability and the dining plan. Additionally, students have reported a lack of privacy, bureaucratic hurdles, and a sense that the university prioritizes financial gain over student well-being. The snippets indicate a need for improvements in communication, support services, and a reevaluation of the value provided for the cost of attendance.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- High tuition costs without perceived corresponding value ("school is very expensive", "tuition is so expensive and just went up").
- Lack of financial aid and transparency in pricing ("extremely stingy financial aid", "they should only charge what the initial price upon entering was and keep that amount").
- Inconsistency in educational quality ("quality varied from professor to professor", "biology department is not gw's strong suit").
- Poor online learning experience ("entire semester was online", "online school is obviously not optimal").
- Lack of school spirit and cohesion ("campus doesn't feel cohesive", "there is very little dignity").
- Expensive housing and dining plans ("off-campus housing is very pricey in d.c", "dining plan in fall 2022...was much better than the current dining plan").
- Bureaucratic and slow administrative response ("administration can be somewhat slow to address issues", "it was nearly impossible to book appointments with advising").
- Perceived lack of support for diversity and inclusion ("however, i am a person of color, and the school's lack of dignity is felt everyday and everywhere i go").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Conduct a thorough review of tuition and fees to ensure they align with the quality of education and services provided.
- Enhance financial aid offerings and improve transparency around costs and billing.
- Implement a robust system for evaluating and improving the quality of education across all departments.
- Reassess the online learning infrastructure and support to ensure it meets student needs and expectations.
- Foster a more cohesive campus culture by promoting school spirit and community-building activities.
- Reevaluate housing and dining options to make them more affordable and appealing to students.
- Streamline administrative processes to be more responsive and efficient in addressing student concerns.
- Develop initiatives to support diversity and inclusion, ensuring all students feel valued and respected.

</ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the George Washington University (GWU) appears to be successfully delivering a high-quality education across various disciplines, with a particular strength in international affairs, political science, and public health. The faculty is noted for their expertise and dedication to student success, with many professors being leaders in their fields. The university's location in Washington D.C. offers unique opportunities for internships and exposure to political and international institutions. Diversity and inclusivity are also highlighted, with a supportive environment for minority and LGBTQ+ students. The transition to online education during the pandemic was well-received, indicating adaptability and commitment to maintaining academic standards. Financial aid and scholarships seem to be effectively supporting students from various economic backgrounds.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- High-quality education in various disciplines (international affairs, political science, public health)
- Expert and dedicated faculty
- Unique opportunities due to location in Washington D.C.
- Supportive environment for diversity and inclusivity
- Successful transition to online education during the pandemic
- Effective financial aid and scholarship programs

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could focus on expanding its efforts to increase diversity within the classroom to reflect the broader D.C. community. 
- Additionally, continuing to develop and promote STEM and engineering programs could attract a wider range of students. 
- Strengthening community spaces and fostering community growth could also enhance the sense of belonging among students. 
- Lastly, maintaining and expanding partnerships with local vendors for the GWorld payment system could continue to provide convenient and affordable options for students.


</ul> 
    </Tab>
</Tabs>