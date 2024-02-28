
**Summary**

Overall sentiment: Positive experiences regarding fun & stress-free dominate, with 63.4% positive to 6.6% negative feedback. Positive reviews: 231; Negative reviews: 109; Neutral reviews: 109


**Positive:**

1. Location Convenience: Guests praised the hotel's proximity to tram and monorail stations, making
travel around Dubai easy.
2. Transport Access: The hotel's location was appreciated for its easy access to public transportation,
including the metro.
3. Amenities Enjoyment: Some guests enjoyed the hotel's amenities, like the pool and gym, and found
them to be great for children.
4. Room Features: A few reviews mentioned well-equipped kitchens and the convenience of having TVs
in every room.
5. Efficient Services: Positive remarks were made about the smooth check-in and check-out process, and
the helpfulness of the travel desk.
 

**Negative:**

1. Limited Entertainment: Guests felt the area lacked nightlife and socializing options, with no bars or
nearby hangout spots.
2. Accessibility Issues: Some found the hotel's location inconvenient, with nothing within walking
distance and difficulties in reaching the hotel by car.
3. Amenities Shortcomings: Complaints about the pool size, lack of sun loungers, and limited TV channel
selections were noted.
4. Service Delays: There were mentions of delays in service and slow responses to requests, impacting
guest satisfaction.
5. Connectivity Concerns: Issues with Wi-Fi speed and connectivity were a point of frustration for some
guests.

<br>

**Most Positive Examples:**

1. "great transport links"
2. "easy access to metro and tram"
3. "hotel is well connected to the public transport tram metro"
4. "location being a very short walk to the tram station"
5. "tram station right in front of the hotel"


**Most Negative Examples:**

1. "hotel has no-where to hang out"
2. "location wasn't great"
3. "swimming pool was affected by the problem"
4. "tv selections in the room is very limited"
5. "very few staff members available during breakfast"

<br>

```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'fun & stress-free'
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
    AND TRIM(Category) = 'fun & stress-free'
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
        "#FF4136", // A shade of red
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>

<br>

**Headlines and corresponding snippets from reviews**

<br>

**Positive Headlines**
```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
  AND TRIM(LOWER(Category)) = 'fun & stress-free'
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



