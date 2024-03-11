# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the snippets regarding safety and security at the university is mixed, with a notable number of positive experiences highlighting the presence of campus police, safety services, and the general feeling of safety, even at night. However, there are also several negative experiences that mention crime alerts, thefts, and the need for vigilance, especially in an urban setting.


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
    AND TRIM(Category) = 'safety'
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
    AND LOWER(TRIM(Category)) = 'safety'
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
- **Campus Presence:**  Students feel reassured by the visible presence of campus police and security measures, such as the GW Police Department (GWPD) and emergency poles.
- **Night Safety:**  Services like campus taxis and the 4ride late-night escort service contribute to a sense of safety during nighttime.
- **Urban Safety:**  Despite being in the heart of Washington D.C., students appreciate the campus's separation from city bustle and the livability of the Foggy Bottom neighborhood.
- **Safety Resources:**  The university's efforts in providing safety information and awareness about issues like sexual assault are recognized and valued.
- **Security Measures:**  The implementation of security in dorms, including access cards and the presence of security personnel, is seen as effective in maintaining a safe environment.

 

## Negative: 
- **Crime Alerts:**  Receiving frequent crime alerts can create an atmosphere of concern and distrust among students.
- **Urban Risks:**  The reality of being in a city with crime and the need for constant awareness, especially at night, is a concern for some students.
- **Theft Incidents:**  Multiple accounts of on-campus robberies and thefts, particularly of laptops, have been reported, raising safety concerns.
- **Security Gaps:**  Some students feel that the security on campus is not as secure as it could be, with non-students occasionally gaining access to dorms.
- **Vigilance Required:**  The need to be observant and to travel in groups at night is emphasized, indicating that safety is not taken for granted.

<br>

## Most Positive Examples:
- "i feel safe walking around at night"
- "there is a lot of gwpd that takes care of the general safety"
- "gw works really hard to provide safety"
- "cops are everywhere in d.c"
- "for an open campus in an urban city, i generally feel safe on campus"


## Most Negative Examples:
- "occasionally i get crime alerts"
- "there have been multiple accounts of on-campus robberies"
- "gwpp came when i pressed the button on the emergency pole"
- "however, safety has been a concern lately as there have been a number of armed robberies, petty thefts, and the like on campus"
- "considering the fact that it is a city, you never know what's coming your way"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
    AND TRIM(LOWER(Category)) = 'safety' -- Change category as needed
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
        
The student reviews indicate a range of concerns regarding safety and security at the university. Issues such as inadequate lighting, visibility of security personnel, and the ease of non-student access to buildings are mentioned. Some students express discomfort with the open campus layout, especially at night, and there are reports of thefts and other crimes. While some students feel that safety measures are somewhat strict and that the university communicates well during incidents, others feel that enforcement is not consistent and that there is a need for increased security presence. The mixed experiences suggest that while some safety protocols are in place, there are areas that require attention to enhance the overall sense of security for students.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Inadequate lighting at shuttle stops ("there should be more light where the vex late-night stop is")
- Non-student access to buildings ("it is easy for people who do not attend here to get into residential buildings")
- Theft incidents ("my friend's laptop and chargers were stolen straight out of her room")
- Perceived lack of strict enforcement ("university does not seem very strict on enforcement")
- Insufficient security presence at night ("I do not see campus police around often at night")
- Open campus layout contributing to safety concerns ("safety is hard at gw because we live right in the city with an open campus")
- Crime in surrounding areas affecting campus safety ("surrounding city area can be unsafe")
- Inconsistent security at building entrances ("some dolls both lack security officers at the front desk and have automatic open doors")
- Reports of serious crimes and frequent alerts ("i often get texts from campus safety about muggings, stabbings, robberies")
- Concerns about safety when traveling alone at night ("but at night i do not enjoy walking back alone")


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Increase lighting in poorly lit areas, especially where students wait for transportation.
- Review and strengthen access control measures to prevent unauthorized entry into buildings.
- Enhance the visibility and presence of security personnel, particularly during nighttime hours.
- Ensure consistent enforcement of safety policies and communicate this clearly to students.
- Explore the possibility of a more secure campus layout or additional measures to mitigate the risks associated with an open campus.
- Provide additional resources and support for students to report safety concerns and receive timely assistance.
- Offer safety education programs to help students navigate the urban environment safely.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university appears to have a robust safety and security system in place, with frequent mentions of the GW police's presence and effectiveness. Students feel safe on campus, even at night, and appreciate the various safety measures, such as the blue light system and the 4-ride escort service. The campus environment is described as safe, well-lit, and well-patrolled by multiple law enforcement agencies. Additionally, the university's location in Washington D.C. provides a wealth of internship and job opportunities, which students find beneficial for their future career prospects. The campus's urban setting does not detract from the sense of security, and the university's efforts to maintain a safe environment are acknowledged by the students.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- GW police presence and availability (e.g., "gw police will be around 24/7", "i constantly see gw police patrolling the area")
- Safe campus environment (e.g., "campus is safe", "felt safe", "safe and friendly environment")
- Safety measures and services (e.g., "blue lights everywhere", "4-ride system is incredibly helpful", "emergency blue light system")
- Internship and job opportunities (e.g., "endless options for internships and jobs", "opportunities for work/internship experience throughout d.c.")
- Location in Washington D.C. (e.g., "location is in the heart of america and american politics", "monuments, white house, and smithsonians are all your fingertips")
- Campus security features (e.g., "campus is well-lit", "security cameras", "security in the dorms is very good")
- Student awareness and education on safety (e.g., "we are all given safety briefs during freshman year")
- Positive campus atmosphere (e.g., "seeing the campus looks very chill and peaceful", "it's a very comfortable atmosphere")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- No suggestions for this satisfaction category.

</ul> 
    </Tab>
</Tabs>