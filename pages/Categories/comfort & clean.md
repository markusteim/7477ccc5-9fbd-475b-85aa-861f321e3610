
<script>
  import noindex from '/Users/markusteimann/Desktop/report 2/components/noindex.svelte';
</script>

**Summary**

Comfort and cleanliness in hotels are pivotal for guest satisfaction. The reviews suggest a predominantly positive sentiment, with approximately 93.42% of guests expressing contentment with their experiences. Positive reviews: 497; Negative reviews: 24; Neutral reviews: 11.

 

**Positive:**

1. Room Quality: Guests raved about the luxurious, spacious rooms with comfortable beds and
high-quality linen, ensuring restful nights.
2. Amenities Galore: Many appreciated the well-equipped rooms with modern appliances, including
washing machines, and the plush toiletries provided.
3. Pristine Cleanliness: The cleanliness of the rooms received high praise, with housekeeping services
lauded for their diligence and consistency in maintaining spotlessness.
4. Facility Excellence: The hotel's facilities, such as the gym and pool, were described as excellent, with
special mentions of the heated infinity pool and the well-maintained amenities.
5. Comfortable Accommodations: The generously sized accommodations were highlighted for their
comfort, with guests enjoying the ample space and the cozy, home-like atmosphere.

**Negative:**

1. Room Discomfort: A few guests found the air conditioning inadequate and the rooms or pool area too
small, leading to a cramped experience.
2. Service Shortcomings: Some guests noted issues with housekeeping, such as infrequent cleaning or
the need for better maintenance in certain areas.
3. Environmental Factors: There were mentions of the hot weather affecting comfort levels, with guests
needing to shower frequently to cool down.
4. Amenity Issues: A minority reported minor inconveniences like leaky showers, dirty windows, and a
lack of towel dryers.
5. Safety Hazards: One guest mentioned slipping on a hard marble floor, indicating a potential safety
concern.

<br>

**Most Positive Examples:**

1. "bed and bedding were of the highest quality"
2. "cleaning was fabulous"
3. "pillows here are heavenly to sleep on"
4. "room was sufficiently quiet, clean and amazing"
5. "they were shining each day"

 

**Most Negative Examples:**

1. "air-conditioning in the room was quite weak"
2. "only let down is rooms are bit cramped"
3. "i saw spots on the table that were not cleaned up"
4. "water leaking through the shower door"
5. "i flipped once on a hard marble floor"

<br>

```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'comfort & clean'
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
AND TRIM(Category) = 'comfort & clean'
GROUP BY
    TRIM(LOWER(Category)),
    polarity
ORDER BY
    Polarity_sum DESC
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
        '#3D9970',  // A shade of dark green
        '#2ECC40',      // A shade of bright green
        '#AAAAAA',       // A shade of grey
        '#FF4136',      // A shade of red
        '#85144B'  // A shade of dark red
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>

<br>

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
  AND TRIM(LOWER(Category)) = 'comfort & clean'
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