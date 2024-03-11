# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the university reviews regarding taste, food, dining, and restaurants presents a dichotomy of experiences. While there is a clear appreciation for the variety and quality of off-campus dining options, including a diverse array of cuisines and vegetarian/vegan choices, the on-campus dining program receives significant criticism for its quality, variety, and cost. The positive remarks often highlight the abundance of nearby restaurants and the convenience of the GWorld food plan, whereas the negative comments focus on the subpar offerings of the on-campus meal plans and dining halls.

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
    AND TRIM(Category) = 'tastes'
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
    AND LOWER(TRIM(Category)) = 'tastes'
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
- **Diverse Cuisines:** Students relish the proximity to a multitude of restaurants serving international flavors like Thai, Indian, and Ethiopian, as well as the availability of vegetarian and vegan options.
- **Off-Campus Variety:** The vast array of off-campus dining options, including food trucks and city-wide eateries, is frequently praised for providing a welcome escape from university food.
- **GWorld Plan:** The GWorld food plan is commended for its flexibility, allowing students to dine at various locations throughout the city, enhancing their culinary experiences.
- **Ethnic Options:** The rich tapestry of D.C.'s ethnic food scene is a highlight for many, with students enjoying the ability to sample global cuisines right at their doorstep.
- **Healthy Alternatives:** The presence of Whole Foods and other health-conscious establishments is noted as a positive aspect, offering students healthier dining choices.
 

## Negative:
- **Poor Quality:** The on-campus dining, particularly the main cafeteria, is repeatedly criticized for its low quality and lack of appetizing options.
- **Limited Variety:** Students express frustration over the limited variety of on-campus food, with some feeling stuck with repetitive and unhealthy choices.
- **Expensive Options:** The cost of dining, especially in downtown D.C., is a concern, with students finding the food overpriced and not always worth the expense.
- **Meal Plan Issues:** The structure and value of the on-campus meal plan are sources of dissatisfaction, with students finding it difficult to make their dining dollars last.
- **Lack of Dining Halls:** The absence of traditional dining halls is a significant drawback for some, leading to a reliance on off-campus dining or self-cooking.

<br>

## Most Positive Examples:
- "huge variety of off campus food"
- "a lot of different types of food"
- "there are tons of vegetarian/vegan options"
- "there are amazing restaurants all over the place"
- "food trucks come through campus usually around lunch time"


 

## Most Negative Examples:
- "terrible food/dining program"
- "our on-campus meal plan is horrible"
- "cafeteria is pretty awful"
- "food sucks"
- "food is pretty subpar"

<br>




<br>

## Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
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
WHERE TRIM(LOWER(Category)) = 'tastes'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
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
WHERE TRIM(LOWER(Category)) = 'tastes'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
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
    AND TRIM(LOWER(Category)) = 'tastes' -- Change category as needed
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
        
The student reviews indicate dissatisfaction with the university's dining services. The primary concerns revolve around the lack of variety, the perceived low quality of food, the absence of a traditional dining hall, and the high cost of available food options. Students express frustration with the repetitiveness of the menu, the limited choices for those with dietary restrictions, and the overall unappetizing nature of the food. The new dining plan has been criticized, and there is a desire for more integration with local grocery stores and restaurants. The issues seem to affect student life significantly, as food is a daily necessity, and the current offerings do not meet the diverse needs and expectations of the student body.
        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Repetitive food options ("food can get old, options are repetitive", "can be repetitive").
- Low quality of food ("food is not good at all", "quality of campus food is garbage", "terrible food/dining program").
- Lack of a traditional dining hall ("since there is no dining hall", "university doesn't have its own dining hall").
- Limited variety and unhealthy options ("variety of food at gw is not great", "there are very few healthy options available").
- High cost of food ("albeit expensive", "food could be less expensive", "most of them are pricy").
- Dissatisfaction with the dining plan ("new dining plan change was a mistake", "no one likes the campus dining plan").
- Limited options for dietary restrictions ("it is hard for vegetarians and vegans to find food", "what i've had is a little limited in what i can eat since i'm gluten-intolerant").
- Desire for more integration with local businesses ("recommend the incorporate more restaurants, grocery stores", "local businesses that take gworld are way better in quality").
- Negative perception of J Street dining ("j street is a poor excuse for a dining hall", "food at j street is not the best").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Introduce more variety in the menu to prevent repetitiveness and cater to diverse tastes.
- Improve the quality of food by sourcing from better suppliers or by enhancing the culinary skills of the dining staff.
- Explore the possibility of establishing a traditional dining hall that offers a wider range of food options.
- Expand the dining plan to include more local restaurants and grocery stores, especially those offering healthy and dietary-specific choices.
- Review and adjust the pricing strategy to make food options more affordable for students.
- Conduct regular surveys or focus groups with students to gather feedback and make continuous improvements to the dining services.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university's dining approach appears to be well-received by students, with a focus on flexibility and variety. The meal plan, referred to as GWorld, is particularly appreciated as it can be used at a wide range of local stores and restaurants, offering students the chance to explore the city's diverse culinary scene. The presence of brand new dining halls and the availability of healthy options, including vegetarian and vegan choices, are also noted positively. The proximity to a multitude of off-campus dining options, including ethnic cuisines and popular chains, contributes to the overall satisfaction with the food experience at the university.
        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Flexibility of meal plan (GWorld) allowing dining at local establishments ("meal plan is a debit card that you can use at local stores/restaurants", "you can use your meal plan at practically any restaurant, deli, and store in the area").
- Variety of food options both on and off-campus ("lot's of variety", "there's something for everyone", "great variety").
- Availability of healthy and dietary-specific options ("food is generally healthy and delicious", "extensive vegan/vegetarian bar", "ton of vegetarian and vegan options").
- Proximity to a wide range of restaurants and eateries ("great restaurants and businesses nearby", "restaurants are good", "there are over 90 places to eat on gworld").
- New dining facilities ("brand new dining halls on both sides of the campus").
- Encouragement to explore local cuisine ("encourages you to try eating at different places", "living in such a diverse city ensures tons of delicious and different options").
- Positive experiences with campus favorites and ethnic food ("tonic, carvings, and chipotle are campus favorites", "love the variety on ethnic food").
- Access to food trucks and farmers markets ("gw campus is the center of the food truck craze", "variety of farmers markets seen throughout the year").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- No suggestions for this satisfaction category.

</ul> 
    </Tab>
</Tabs>