


**Summary**

Sound experiences in hotels often make or break a guest's stay. In this case, the sentiment leans positively, with  45.95% of guests enjoying sound-related aspects, while 45.94% faced disturbances. Positive reviews numbered 17, while negative feedback came from 17 individuals and neutral feedback came from 3 individuals.

 

**Positive:**

1. Soundproofing Success: Guests praised the effective soundproofing, noting that even rooms facing the
road remained undisturbed.
2. Silent AC: The air conditioning was commended for its silent operation, contributing to a clean and
quiet room atmosphere.
3. Insulated Interiors: Many found the rooms to be very quiet, with excellent sound insulation ensuring a
peaceful environment.
4. Audio Amenities: In-room audio features like Bluetooth speakers and smart TVs enhanced the
enjoyment of personal music without noise issues.
5. Construction Immunity: Despite nearby construction, guests reported a quiet and undisturbed stay,
with no impact on their comfort.
 

**Negative:**

1. Room Shortcomings: Some guests mentioned low TV volume and curtains that didn't close properly,
affecting room comfort.
2. Noise Nuisances: Complaints included construction noise, loud fridges, and noisy housekeeping in the
corridors.
3. Disturbance Displeasure: Guests were disturbed by demolition sounds, road noise, and parties in
adjacent rooms.
4. Unwanted Wake-ups: Early morning construction and inadequate curtains led to guests being woken
up too early.

<br>

**Most Positive Examples:**

1. "windows faced the road but they were soundproof"
2. "air conditioning was working perfectly and silently"
3. "rooms have excellent sound insulation"
4. "bluetooth speaker"
5. "noiseless no disturbances"

 
**Most Negative Examples:**

1. "construction noise from the building next door"
2. "firdge is very loud"
3. "noise caused by the demolition of a building around the hotel"
4. "outside noise"
5. "rooms are not as quiet"

<br>

```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'sound'
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
WITH Polarity_Ordered AS (
  SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    COUNT(CAST(value AS INTEGER)) AS Polarity_sum,
    CASE
      WHEN TRIM(LOWER(polarity)) = 'very negative' THEN 1
      WHEN TRIM(LOWER(polarity)) = 'negative' THEN 2
      WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 3
      WHEN TRIM(LOWER(polarity)) = 'positive' THEN 4
      WHEN TRIM(LOWER(polarity)) = 'very positive' THEN 5
      ELSE 6
    END AS OrderIndex
  FROM
    hotels.titles
  WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'sound'
  GROUP BY
    TRIM(LOWER(polarity))
)

SELECT
  Polarity,
  Polarity_sum
FROM Polarity_Ordered
ORDER BY OrderIndex

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
/>

<br>


**Headlines and corresponding snippets from reviews**

**Positive Headlines**
```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sound'
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
WHERE TRIM(LOWER(Category)) = 'sound'
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
WHERE TRIM(LOWER(Category)) = 'sound'
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
WHERE TRIM(LOWER(Category)) = 'sound'
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
WHERE TRIM(LOWER(Category)) = 'sound'
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
WHERE TRIM(LOWER(Category)) = 'sound'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>


**Customer sentiment distribution (2022-2023)**

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
/>