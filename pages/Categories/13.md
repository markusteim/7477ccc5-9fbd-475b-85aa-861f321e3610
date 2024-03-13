# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*





# Summary
Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 

The overall sentiment from the snippets regarding the aesthetic appreciation of material things at the university leans more towards the positive. Students frequently praise the prime location, modern facilities, and beautiful architecture. However, there are recurring complaints about older buildings, lack of green spaces, and ongoing construction.


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
    AND LOWER(TRIM(Category)) = 'aesthetics'
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
    AND LOWER(TRIM(Category)) = 'aesthetics'
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

<br>



<br>


## Positive:
- **Urban Beauty**: The university's urban and modern campus is lauded for its unbeatable location, with proximity to the White House and National Mall, and views of iconic monuments from rooftop areas.
- **Housing Quality**: Newly built or renovated dorms like West Hall and Somers Hall receive high marks for their impressive standard of living, while the quality of nearby off-campus housing is also praised.
- **Seasonal Charm**: Students find the city beautiful throughout the year, with spring and fall being particularly stunning due to the blossoming cherry trees and colorful leaves.
- **Facility Upgrades**: The university is actively renovating and rebuilding dorms and academic buildings, leading to state-of-the-art facilities and well-maintained athletic and student centers.
- **Cultural Richness**: The proximity to museums, monuments, and historical neighborhoods enriches the student experience, with many finding inspiration in the surrounding areas.



## Negative:
- **Building Conditions**: Some buildings are described as old, dirty, moldy, and in need of renovation, with issues like black mold and ceiling collapses reported.
- **Greenery Scarcity**: A major lack of green spaces on campus is a common complaint, with the urban setting offering limited areas for relaxation and recreation.
- **Construction Disruption**: Ongoing construction is a source of inconvenience, with some students feeling that it detracts from the campus's aesthetic appeal.
- **Aesthetic Inconsistency**: While some facilities are extravagant, others are considered average or outdated, leading to a mixed experience of the campus environment.
- **Dorm Disparities**: Freshmen and some upperclassmen face the challenge of being placed in older, less appealing dorms, contrasting sharply with the nicer housing options available to others.


<br>

## Most Positive Examples:
- "beautiful city with a ton of attractions"
- "neoclassic D.C. buildings create amazing campus environment"
- "beautiful campus"
- "museums and monuments are breathtaking and plentiful"
- "beautiful edwardian-era houses, embassies and consulates, diplomatic residences"

 

## Most Negative Examples:
- "our building for public health is old"
- "gelman looks hideous"
- "library really needs work"
- "most buildings are decrepit"
- "dorms are old and falling apart"


# Headlines and corresponding snippets from reviews 

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
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
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
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
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
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
    AND TRIM(LOWER(Category)) = 'aesthetics' -- Change category as needed
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
The student reviews indicate a range of concerns regarding the physical state and aesthetics of the university's facilities. The primary issues seem to revolve around outdated and poorly maintained buildings, including dormitories and academic facilities. There is a notable dissatisfaction with the lack of green spaces and the condition of the housing options, with many students describing them as old, dirty, and in some cases, unsafe due to structural issues like ceiling collapses. The library and certain halls like Gelman, Funger, and Phillips are specifically mentioned as needing renovations. Additionally, there is a sentiment that the university's urban setting limits the availability of spacious and attractive housing and green areas.



        <br>
        <br>

<b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Unorganized aesthetics (really unorganized)
- Dining and housing situations need change (things i would like to see change are the dining and housing situations)
- Outdated buildings (certain buildings are extremely outdated)
- Need for renovations in classroom facilities (i am sure the university has the funds to renovate and improve certain classroom facilities such as Funger and Phillips hall)
- Lack of inspiration from campus and surrounding area (campus and surrounding area don't really inspire)
- Mediocre facilities (everything else at the school is mediocre)
- Run-down and gross dormitories (others, like Thurston, are a kind of run down and gross)
- Less than ideal facilities on Mount Vernon campus (facilities on the Mount Vernon campus are less than ideal)
- Decrepit buildings (most buildings are decrepit)
- Older dorms need improvements (older dorms could definitely use more improvements)
- Lack of green spaces (major lack of green spaces)
- Old and moldy buildings (building is dirty and moldy)
- Issues with black mold and structural integrity (we've had ceiling collapses, bathtubs breaking, black mold issues)
- Library in need of remodeling (library is in the process of being remodeled because it is a bit old)
- Outdated computer lab equipment (computer lab equipment is pretty old)
- Limited green space (there is a limited amount of green space)
- Outdated housing options (some of these off-campus housing options were once hotels)
- Disparity in dorm quality (half of the dorms are nice and half are old and really run-down)
- Need for renovation in some buildings (there are some old buildings that really need to be renovated)
- Gelman Library is outdated (gelman library is outdated)


</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address the concerns raised in the student reviews, the university should prioritize the renovation of outdated buildings, particularly those frequently mentioned like Gelman, Funger, and Phillips halls. 
- Efforts should be made to improve the condition of older dormitories, with a focus on eliminating mold and ensuring structural safety. 
- The university could also explore ways to increase green spaces, even in its urban setting, to enhance the campus's aesthetic appeal. 
- Upgrading the computer lab equipment and ensuring that all facilities are well-maintained would likely improve overall student satisfaction. 
- Additionally, the university might consider offering a wider range of housing options to accommodate different preferences and budgets.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university receives commendation for its prime location, with many students appreciating the proximity to significant historical sites and the convenience it offers for various majors. The campus aesthetics are frequently praised, with mentions of modernized technology, renovated dormitories, and well-maintained buildings. The university's efforts in updating facilities, including libraries and athletic centers, have not gone unnoticed. The availability of diverse housing options, including apartment-style dorms with private bathrooms and kitchens, contributes to student satisfaction. The presence of green spaces and the university's maintenance of its physical environment also add to the overall positive experience.


        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Prime location with historical significance (e.g., "proximity to the national monuments," "walking distance from the white house and world bank")
- Campus aesthetics and maintenance (e.g., "beautiful campus," "modernized technology," "renovated dormitories")
- Diverse and high-quality housing options (e.g., "apartment-style dorms," "private bathrooms," "full kitchen")
- Updated facilities (e.g., "state-of-the-art libraries," "recently renovated athletic centers")
- Green spaces and environmental upkeep (e.g., "gw landscapes really well around its campus," "green space can be found")

</ul>

<br>

<b>Suggestions:</b>
<br>

<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could continue its renovation projects to ensure all facilities remain modern and functional. 
- Additionally, maintaining the balance between urban convenience and green spaces will be crucial for the well-being of students. 
- Expanding on the diversity of housing options and ensuring that all students have access to high-quality accommodations could also be beneficial. 
- Keeping the campus clean and well-maintained, especially during construction phases, will help preserve the positive aesthetic impressions.

</ul> 
    </Tab>
</Tabs>