

# Summary

Overall **<Value data={polarity_proportions} column=percentage row=2/>%**  of customers had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** of customers who had a **negative experience**.


**Negative** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 

```sql polarity_proportions

WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN TRIM(LOWER(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN TRIM(LOWER(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'taste'
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
        COUNT(DISTINCT review_id) AS Polarity_sum
    FROM
        hotels.titles
    WHERE travel_date BETWEEN '2022-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'taste'
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

1. Breakfast Delights: Guests rave about the "best breakfast ever," with a "superb choice" and "dreamy"
inclusive options.
2. Culinary Excellence: The hotel's restaurant impresses with "five-star meals," "fantastic buffets," and
"most delicious mushroom soup."
3. Special Offers: Promotions like the "best wine deal" add value to guests' dining experiences.
4. Diverse Options: The variety at breakfast and dinner is celebrated, with "amazing spreads" and
"delicious and varied" buffets.
5. Personalized Service: Chefs accommodate specific requests, like cooking "specific types of eggs,"
enhancing the personalized dining experience.
 

## Negative:

1. Vegetarian Concerns: Some guests note a lack of vegetarian options at dinner and a desire for more
variety.
2. Quality Issues: There are mentions of "low-quality bread," "stale pastries," and "almost raw scrambled
eggs," indicating room for improvement.
3. Dietary Restrictions: Vegan guests express disappointment with the limited options and labeling for
vegan dishes.
4. Freshness Lapses: Comments about "cold" poached eggs and "not much refreshing" juices suggest
some inconsistencies in food preparation.
5. Hygiene Preference: One guest suggests that single portions might be more hygienic than the current
buffet setup.

<br>

## Most Positive Examples:

1. "best breakfast i have ever had"
2. "if you want a specific type of eggs, the chef will cook it for you"
3. "most delicious mushroom soup ever"
4. "breakfast was just superb"
5. "we had breakfast inclusive and its like in a dream"

 

## Most Negative Examples:

1. "dinner sometimes lacked vegetarian options"
2. "bread was low in quality"
3. "freshly made poached eggs are cold inside"
4. "good breakfast but the pastries could be fresher than stale"
5. "i still don't know why almost every buffet in dubai has almost raw scrambled eggs"

<br>




<br>

## Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
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
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
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
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'taste'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
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

# Customer sentiment distribution (2022-2023)

```sql sentiment_distribution
WITH Polarity_Ordered AS (
  SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    Year, -- Extract the year from the travel_date
    COUNT(DISTINCT review_id) AS ReviewCount, -- Count unique review IDs
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
    travel_date BETWEEN '2022-01-01' AND '2023-12-31'
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
        
        The feedback from guests indicates that the hotel's culinary offerings have room for
improvement in several areas. Specific concerns have been raised about the quality
of bread, the freshness of pastries, and the temperature of poached eggs.
Additionally, guests have noted a lack of variety and options for those with dietary
restrictions, such as vegetarians and vegans. The repetition of dishes and the
presentation of food items, such as Nutella and jams in jars, have also been
mentioned. The comments suggest that guests are seeking more hygienic serving
methods and a more refreshing beverage selection from the bar. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Low-quality bread (bread was low in quality)</li>
<li>Insufficient vegetarian options and repetitive dinner menus (dinner sometimes
lacked vegetarian options and dishes were repeated some nights)</li>
<li>Overall food quality not meeting expectations (food could have been better)</li>
<li>Limited options for specific dietary needs (for us, the breakfast was
disappointing for vegan options and labelling)</li>
<li>Poached eggs served cold (freshly made poached eggs are cold inside)</li>
<li>Stale pastries (good breakfast but the pastries could be fresher than stale)</li>
<li>Preference for single-serve portions (i still think having single portions is more
hygienic)</li>
<li>Beverages not refreshing (juices from the bar were not much refreshing)</li>
<li>Disappointing dinner experience (our second night’s dinner, it was really
disappointing)
10.Frequent occurrence of stale pastries (pastries are often stale)
11.Lack of food choices (tip: maybe add more choices to the food)
12.Questioning the appeal of certain buffet items (what&#39;s the deal with that and
who eats that</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To address these concerns, the hotel could consider the following
improvements:</li>
<li>Source higher-quality bread and ensure pastries are fresh daily.</li>
<li>Expand the menu to include a wider variety of vegetarian and vegan options,
and ensure these are clearly labeled.</li>
<li>Introduce a more diverse dinner menu to avoid repetition.</li>
<li>Serve poached eggs and other hot items at the correct temperature.</li>
<li>Consider offering single-serve portions for spreads like Nutella and jams to
enhance hygiene.</li>
<li>Review the beverage selection at the bar to ensure drinks are appealing and
refreshing.</li>
<li>Collect guest feedback on specific buffet items to understand preferences and
adjust offerings accordingly.</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        The hotel's culinary offerings seem to be a strong point, with numerous guests
expressing satisfaction with the variety and quality of food available, particularly at
breakfast. The breakfast buffet is frequently highlighted for its extensive selection,
catering to a wide range of dietary preferences and tastes. Guests appreciate the
freshness and quality of the food, as well as the availability of different cuisines. The
restaurant, Seven Seeds, is mentioned positively for its value and variety. The
availability of half-board options, the convenience of in-room dining, and the
presence of a supermarket nearby for essentials are also noted. Overall, the hotel's
food services appear to be well-received by guests, with specific commendations for
certain dishes and the chefs who prepare them 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Extensive breakfast buffet with a variety of options (e.g., &quot;breakfast buffet
spread,&quot; &quot;huge variety,&quot; &quot;breakfast buffet beautiful&quot;).</li>
<li>Quality and freshness of food (e.g., &quot;all food was super fresh, super
delicious,&quot; &quot;always fresh&quot;).</li>
<li>Variety of cuisines and dishes available (e.g., &quot;authentic Lebanese food,&quot;
&quot;dishes from around the world&quot;).</li>
<li>Special mentions of chefs and specific dishes (e.g., &quot;best chefs Rajesh, Lovin,
Danaska, Bryan,&quot; &quot;amazing culinary experience&quot;).</li>
<li>Availability of half-board options (e.g., &quot;half board which is worth it&quot;).</li>
<li>In-room dining and convenience of nearby supermarket (e.g., &quot;in-room dining
was very nice,&quot; &quot;small supermarket/convenience store opposite&quot;).</li>
<li>Special dietary needs catered for (e.g., &quot;with options for different dietary
preferences,&quot; &quot;many vegetarian food options in breakfast&quot;).</li>
<li>Positive experiences with specific meals beyond breakfast (e.g., &quot;dinner was
always a buffet,&quot; &quot;afternoon tea was the best value&quot;).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To further enhance the guest experience, the hotel could consider gathering
specific feedback on favorite dishes or any areas for improvement in the
culinary offerings. Additionally, promoting the variety and quality of the food in
marketing materials could attract guests who prioritize dining experiences.</li>
<li>Ensuring consistent quality across all meals and continuing to cater to diverse
dietary needs will help maintain the high level of guest satisfaction.</li>


    </ol> 
    </Tab>
</Tabs>