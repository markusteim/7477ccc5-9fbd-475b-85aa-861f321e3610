# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary 

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


In the collected university reviews, the overall sentiment regarding empowerment and access to opportunities is overwhelmingly positive. Students frequently highlight the university's proximity to powerful institutions, networking groups, and career fairs, which seem to foster a sense of empowerment and potential for leadership. The negative aspects are less about empowerment and more about social dynamics and administrative issues.


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
    AND TRIM(Category) = 'empowerment, success & influence'
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
    AND LOWER(TRIM(Category)) = 'empowerment, success & influence'
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
- **Networking Opportunities:** Students feel empowered by the university's connections to prestigious organizations like the IMF and World Bank, and the abundance of networking groups and career fairs.
- **Internship Access:** The proximity to Washington D.C. is repeatedly praised for providing unparalleled internship opportunities, with many students securing positions in government, think tanks, and international affairs.
- **Professional Growth:** The presence of notable professionals and high-quality professors in the field of international affairs is seen as a significant advantage, contributing to students' professional development and sense of importance.
- **Career Services:** The university's career services are lauded for effectively guiding students towards success, with a focus on internships and job placements that make students feel valuable and influential.
- **Greek Life Influence:** Greek organizations are recognized for their networking benefits and leadership roles on campus, suggesting a position of strength for members.


## Negative:
- **Social Attitudes:** Some students perceive a pompous and superior attitude among peers, which can detract from the sense of community and empowerment.
- **Administrative Barriers:** Complaints about red tape, strict parking regulations, and unaccommodating financial counselors suggest challenges in feeling supported by the university.
- **Technology Issues:** Fluctuating internet speeds and network issues are minor but notable frustrations that can impede students' sense of control and efficiency.
- **Academic Rigidity:** A lack of decision-making freedom and inflexible professors can make students feel disempowered in their academic journey.
- **Social Exclusivity:** Greek life's dominance is seen as creating a divide on campus, with non-Greek students feeling marginalized.



<br>

## Most Positive Examples:
- "increases possibilities in the labor market"
- "guiding students along their journeys to success"
- "professors are all high up and educated in the field"
- "internships (at least those for credit) and research opportunities are mostly reserved for upperclassmen"
- "transition successfully into the workforce"
 

## Most Negative Examples:
- "everyone is very pompous"
- "university is more concerned about punishing students and fining them"
- "quite a few people are rude and shallow"
- "however, the lack of decision-making freedom is displeasing"
- "i've genuinely never seen a group of people with less courtesy or manners to staff"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
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
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
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
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
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
Student sentiment distribution (2020-2023)

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
    AND TRIM(LOWER(Category)) = 'empowerment, success & influence' -- Change category as needed
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
        
The university appears to be facing challenges in fostering a positive and inclusive campus culture. Student reviews indicate dissatisfaction with the behavior and attitudes of both peers and staff, including financial counselors, professors, and administrative personnel. The academic environment is described as impersonal and sometimes hostile, with reports of arrogance and a lack of courtesy. Additionally, there are logistical issues affecting student life, such as unreliable internet connectivity, transportation services, and strict enforcement of campus policies. The social atmosphere is perceived as exclusive, with Greek life dominating the social scene and creating a divide among students. These issues suggest a need for the university to address the quality of interpersonal interactions and to improve the support systems in place for students.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Disrespectful financial counselors ("mean financial counselors").
- Lack of courtesy and manners from staff ("i've genuinely never seen a group of people with less courtesy or manners to staff").
- Impersonal academic advisement ("academic advisement is terrible and so impersonal").
- Classist and rude behavior from students ("lots of students here are well off new england wasps that are often rude to staff like janitors, cashiers, etc").
- Unreliable student network ("general- student (gwireless) network will terminate its users sessions every 1-3 hours").
- Poor quality of teaching ("there are a lot of horrible professors").
- Lack of accommodation from professors ("some profs were not accommodating").
- Arrogance among faculty ("many of them are over confident, arrogant, and talk without thinking").
- Strict TA policies ("only had one incident with a t.a. who insisted that cameras stay on").
- Inadequate networking opportunities ("networking is okay but it is nothing remotely impressive").
- Feeling marginalized ("it is easy to be pushed aside").
- Pompous campus culture ("everyone is very pompous").
- Rude departmental call services ("many other departments have rude call services").
- Party crackdowns by university police ("upd tries to break up any on-campus party").
- Shutdown of popular student organizations ("lots of the more popular organizations on campus are being shut down").
- Crackdown on Greek life ("gw administration is seriously cracking down on greek life").
- Inflexible administration ("hard to accomplish what you want with administration").
- Bureaucratic obstacles ("lots of red tape").
- Punitive university policies ("university is more concerned about punishing students and fining them").
- Excessive requirements ("there are a lot of requirements").
- Difficulty obtaining internships ("haven't been able to get an internship yet").
- Strict parking regulations ("gw regulates parking heavily").
- Subpar language department ("language department is not as good as it could be").
- Peer pressure related to drinking ("there is also alot of peak pressure for drinking").
- High levels of substance use ("there is a high level of drinking and drug use on the gwu campus").
- Inconsistent internet speeds ("my internet speed fluctuates often").
- Social exclusivity ("quite a few people are rude and shallow").
- Objectification of women ("guys treat girls like objects").
- Greek life dominance ("greek life has taken over so much that anyone who isn't in it feels outcasted").
- Inflexible class rules ("they are also not very flexible when it comes to rules or guidelines of their class").
- Lack of concern for student understanding ("unfortunately many are too full of themseves to care whether or not you understand the material").
- Inadequate health services ("i highly advise setting up another doctor outside of the university").
- Rude transportation service ("drivers can be rude").
- Unreliable transportation service ("left standing on a dark street corner in the rain for over an hour").
- Limited decision-making freedom ("however, the lack of decision-making freedom is displeasing").
- Scarcity of paid internships ("paid internships are hard to come by").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Conduct training programs to enhance the professionalism and courtesy of staff and faculty.
- Develop a more personalized and supportive academic advisement system.
- Foster a culture of respect and inclusivity through campus-wide initiatives and workshops.
- Improve the reliability and quality of the student network infrastructure.
- Evaluate and address the quality of teaching, including providing support for professors to improve their pedagogical skills.
- Create more robust networking opportunities for students, including career fairs and alumni events.
- Review and potentially revise strict policies that negatively impact student life, such as TA camera requirements and party regulations.
- Increase transparency and reduce bureaucratic hurdles in administrative processes.
- Expand support for students seeking internships, including partnerships with local businesses and organizations.
- Reassess parking and transportation policies to better accommodate student needs.
- Enhance the language department and other academic programs based on student feedback.
- Address substance use issues with targeted education and support services.
- Promote a more inclusive social environment, particularly in relation to Greek life.
- Ensure that health services are adequate and accessible to all students.
- Improve the reliability and customer service of campus transportation options.
- Encourage faculty to be more flexible and understanding of student needs.
- Provide more opportunities for student input and decision-making in university policies.

</ul> 

    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university appears to be effectively leveraging its strategic location in D.C. to provide students with a wealth of professional opportunities, particularly in the fields of international relations, political science, and government. Students express appreciation for the extensive networking possibilities, career-oriented environment, and the university's connections with influential organizations and professionals. The emphasis on internships and career services seems to be a significant factor in students feeling empowered and prepared for the workforce. The presence of career-oriented student organizations and the availability of career fairs and workshops further contribute to the students' sense of being in a position of strength regarding their future careers.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Strategic location in D.C. ("there is no better place for international relations than d.c", "gw's location and professional opportunities on-campus are top-notch")
- Extensive networking opportunities ("great network with professionals in d.c. and beyond", "networking opportunities were endless")
- Career-oriented environment ("students are very career-oriented", "great opportunities to connect you to the real world")
- Internship and job opportunities ("internship opportunities and connections to both the public and private sector are plentiful", "internship opportunities that set me up for my future")
- Career services and support ("effective career services", "transition successfully into the workforce")
- Access to influential professionals and organizations ("i have met ambassadors", "connected with world bank employees")
- Professional development programs ("best decision i made was joining the university honors program", "excellent pre-professional development opportunities")
- Encouragement of student independence ("i actually love the independence that this school offers")
- Quality of faculty ("we have the best professors ever", "professors are incredible, intelligent, and often trailblazers in their own fields")
- Emphasis on real-world experience ("greatest reward of gwu will be the internship and professional opportunities in d.c", "being in the heart of d.c. has opened up so many opportunities for me")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could consider expanding its career services to include more personalized career coaching and mentorship programs. 
- Additionally, ensuring that internship and job opportunities are accessible to students from all academic disciplines could broaden the sense of empowerment across the student body. 
- It may also be beneficial to increase the visibility and accessibility of the university's networking events and career workshops to ensure that all students are aware of and can take advantage of these resources. 
- Lastly, fostering a culture of peer-to-peer networking and alumni engagement could further strengthen the university's community and support system.


    </ul> 
    </Tab>
</Tabs>