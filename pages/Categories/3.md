# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The university reviews present a mixed bag of sentiments regarding sexual experiences on campus. While some students find the dating scene vibrant and full of opportunities, others express frustration with the lack of serious relationships and the prevalence of negative behaviors. The positive remarks seem to focus on the availability and attractiveness of potential partners, whereas the negative comments highlight issues such as sexual harassment and a skewed gender ratio.



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
    AND TRIM(Category) = 'sexual'
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
    AND LOWER(TRIM(Category)) = 'sexual'
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
- **Campus Dating:** Relationships are common, with many students finding partners and enjoying the dating scene. Some even describe the university as a "gold mine" for straight guys, with plenty of attractive girls available.
- **Attractiveness:** Students often praise the physical appearance of their peers, noting that there are "exceptionally hot" individuals, well-dressed students, and a general sense of fashion and attractiveness among the population.
- **Variety & Options:** There is a diversity of students, and many find it easy to find attractive partners. The variety extends to fashion sense, with every day being likened to a "mini-fashion show."
- **Exclusivity:** Some students have found exclusive relationships, indicating that not all interactions are casual. There are mentions of committed couples and exclusive dating.
- **External Dating:** Students also date outside the university, with girls dating guys from nearby Georgetown, suggesting a broader dating pool beyond campus boundaries.

 

## Negative:
- **Negative Comments:** Students have heard sexist and racist comments, indicating an undercurrent of discrimination that affects the social atmosphere.
- **Limited Choices:** Complaints about the lack of "hot, smart, and charming guys" and the perception that girls are not as pretty suggest dissatisfaction with the dating pool's quality.
- **Serious Relationships:** There's a sentiment that serious relationships are rare, with many students engaging in casual hook-ups or avoiding dating altogether.
- **Sexual Misconduct:** Reports of sexual harassment, date rape, and other forms of sexual assault paint a concerning picture of campus safety and respect.
- **Gender Imbalance:** The skewed guy-to-girl ratio is seen as a disadvantage for straight girls looking for straight guys, and the presence of "players" or those already taken further complicates the dating scene.


<br>

## Most Positive Examples:
- "relationships are pretty common at gw"
- "my boyfriend and i have been exclusive for a few months"
- "if you're a guy, gw is gold mine"
- "girls are pretty, guys are cute"
- "students are extremely well dressed"


## Most Negative Examples:
- "heard a lot of sexist and racist comments"
- "date rape happens at every college"
- "a lot of sexual harassment on campus"
- "rape culture is very prevalent on campus"
- "there was an issue with a man looking into the women's restroom from outside at one of the halls"
<br>





# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
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
WHERE TRIM(LOWER(Category)) = 'sexual'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
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
WHERE TRIM(LOWER(Category)) = 'sexual'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
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
    AND TRIM(LOWER(Category)) = 'sexual' -- Change category as needed
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
        
The student reviews suggest a concerning atmosphere regarding sexual experiences on campus. Issues range from sexual harassment and assault to a perceived imbalance in the dating scene. There are reports of voyeurism, a prevalent rape culture, and negative experiences among peers. Some students have felt unsafe, being followed or subjected to sexist and racist comments. The reviews also reflect dissatisfaction with the social dynamics, including a skewed gender ratio and a culture that seems to favor casual encounters over relationships, with a sentiment that finding a desirable and serious partner is challenging.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Sexual harassment on campus ("a lot of sexual harassment on campus").
- Voyeurism incident ("there was an issue with a man looking into the women's restroom").
- Rape culture ("rape culture is very prevalent on campus").
- Negative sexual experiences among students ("many of my peas have had very negative experiences").
- Safety concerns ("i've been followed before").
- Sexist and racist comments ("heard a lot of sexist and racist comments").
- Lack of relationships ("relationships, those are rare").
- Prevalence of sexual assault ("there are plenty of sexual assault").
- Gender ratio imbalance ("guy to girl ratio is not good for girls").
- Disparaging views on attractiveness and eligibility ("very few people are in a relationship", "guys are not overwhelmingly hot", "there are very little hot, smart and charming guys").
- Casual relationship culture ("mostly you just have people looking for hook-ups and booty calls").
- Challenges in finding serious relationships ("no one is interested in serious relationships").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these issues, the university could implement a comprehensive sexual assault prevention and response program, including mandatory training for all students and staff. 
- Enhancing security measures, such as improved lighting and increased campus patrols, could help students feel safer. 
- The university should also establish clear reporting mechanisms for harassment and assault, and ensure that support services for victims are readily accessible and well-publicized. 
- To foster a healthier social environment, the university could promote events and organizations that encourage meaningful connections beyond the hookup culture. 
- Additionally, offering workshops on healthy relationships and consent could help shift campus culture towards more respectful and safe interactions.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the student reviews, it appears that the university has a diverse and active social scene with a focus on appearance and relationships. Students note a variety of individuals in terms of looks and personality, with an emphasis on the ease of finding dating and hookup opportunities. The reviews suggest that there is a general satisfaction with the attractiveness of the student body and the availability of potential partners. The social dynamics within sororities and fraternities are also mentioned, indicating an inclusive environment for different types of individuals. Overall, the reviews reflect a student body that is engaged in the social aspects of university life, with a particular focus on relationships and physical appearance.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Diversity in student body ("there's a variety in the types of guys though", "fraternities take all types of guys", "there are a variety of girls at gw")
- Attention to appearance ("students are extremely well dressed", "everyone looks spiffy and put together", "every day is a mini-fashion show")
- Active dating scene ("there are plenty of good options, as far as dating and hooking up", "relationships are pretty common at gw", "it's pretty easy to find attractive guys")
- Inclusivity in social groups ("for sororities, if you're pretty and have a cool personality it's easy to get into a top house")
- General attractiveness of the student body ("for the most part, the girls are hot", "generally a good looking bunch", "girls are crazy hot here")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could consider implementing workshops or seminars on healthy relationships and consent to ensure that the active dating scene is accompanied by a strong understanding of respectful and safe interactions. 
- Additionally, promoting a more inclusive environment that values diversity beyond physical appearance could contribute to a more well-rounded social experience for all students. 
- Encouraging student-led initiatives that focus on personality, talents, and intellectual pursuits might also provide a counterbalance to the emphasis on looks and dating.

</ul> 
    </Tab>
</Tabs>