<script>
  import noindex from '/Users/markusteimann/Desktop/report 2/components/noindex.svelte';
</script>

**Summary**

Overall Sentiment: The majority of reviews reflect a positive sentiment regarding physical and mental well-being, with approximately 89.38% positive experiences. Positive: 101, Negative: 18, Neutral 1.



**Positive:**

1. Gym Facilities: Guests praised the 24/7 gym, highlighting the variety of cardio equipment and weights,
and the inclusion of a sauna.
2. Relaxation Spots: The infinity pool and sun deck were noted as havens for relaxation, with comfortable
poolside beds readily available.
3. Wellness Options: The availability of a healthy food section at breakfast and the presence of a spa
center contributed to guests' well-being.
4. Comfortable Stays: Many found their rooms and the hotel environment comfortable and relaxing,
likening it to a home away from home.
5. Location Benefits: The hotel's location facilitated relaxing walks to the marina, harbour, and beach,
enhancing guests' overall well-being.
 

**Negative:**

1. Limited Relaxation: Some guests found no comfortable common area to relax in, affecting their ability
to unwind.
2. Environmental Issues: A few guests mentioned sunburn risks and sleep disturbances due to traffic
noise.
3. Health Concerns: There were instances of guests falling ill or requiring medical assistance during their
stay.
4. Gym Space: A minority of guests felt the gym was a bit small and lacked sufficient equipment for their
workouts

<br>

**Most Positive Examples:**

1. "best-class gym facility with studio"
2. "healthy food section is offered at the breakfast"
3. "infinity pool and sun deck provides a haven for relaxation"
4. "environment set of the restaurant is very very calm & relaxing"
5. "we felt confortable all the time"

 

**Most Negative Examples:**

1. "no comfortable common area to relax in"
2. "sleep disturbed by all the traffic"
3. "fell ill during my stay"
4. "gym equipment and space is not enough"
5. "could not lay and relax by pool and could hear from room"

<br>

```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'well-being'
    GROUP BY
        TRIM(polarity)
),
TotalReviews AS (
    SELECT
        SUM(category_count) AS total
    FROM
        CategoryCounts
)
SELECT
    a.CleanCategory AS Category,
    a.category_count,
    ROUND((a.category_count * 100.0) / b.total, 2) AS percentage
FROM
    CategoryCounts a, TotalReviews b
ORDER BY percentage DESC
```

```sql sum_by_polarity
SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    COUNT(CAST(value AS INTEGER)) AS Polarity_sum,
    polarity
FROM
    hotels.titles
WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
AND TRIM(Category) = 'well-being'
GROUP BY
    TRIM(LOWER(Category)),
    polarity

```
<BarChart 
    data={sum_by_polarity} 
    swapXY={false}
    x="Polarity"
    y="Polarity_sum" 
    series="Polarity"
    colorPalette={[
        '#3D9970',  // A shade of dark green
        '#2ECC40',  // A shade of bright green
        '#AAAAAA',  // A shade of grey
        '#FF4136',  // A shade of red
        '#8b0000'   // A shade of dark red
    ]}
/>


<br>



**Headlines and corresponding snippets from reviews**

**Positive Headlines**
```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{positive_headlines}" search="true" rows=40 rowShading=true/>

**Positive Snippets**
```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

**Neutral Headlines**
```sql neutral_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{neutral_headlines}" search="true" rows=40 rowShading=true/>

**Neutral Snippets**
```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>

**Negative Headlines**
```sql negative_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{negative_headlines}" search="true" rows=40 rowShading=true/>

**Negative Snippets**
```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>


**Customer sentiment distribution (2022-2023)**

```sql sentiment_distribution
SELECT
  TRIM(LOWER(polarity)) AS Polarity,
  Year,
  COUNT(DISTINCT review_id) AS ReviewCount
FROM
  hotels.titles
WHERE
  travel_date BETWEEN '2022-01-01' AND '2023-12-31'
  AND TRIM(LOWER(Category)) = 'well-being'
GROUP BY
  Polarity,
  Year

```

<BarChart 
    data={sentiment_distribution} 
    x="Polarity" 
    y="ReviewCount"
    series="Year" 
    groupBy="Year" 
    type="grouped"
/>