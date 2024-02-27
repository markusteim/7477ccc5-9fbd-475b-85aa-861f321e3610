

**Summary**

Overall sentiment leans heavily towards the positive, with approximately 78% of guests expressing satisfaction with the non-material values and value for money of the hotel. Positive reviews: 78; Negative reviews: 22; Neutral reviews: 0;



**Positive:**

1. Ethical Upgrades: Guests appreciated complimentary services like free shuttle, water, and room upgrades without extra costs.
2. Value for Money: Many found the hotel to offer exceptional value, with reasonable pricing and deals that exceeded expectations.
3. Efficient Services: Punctuality and efficiency were highlighted, with on-time services and high-speed internet receiving praise.
4. Language Proficiency: The multilingual communication skills of the staff were noted as a positive aspect, enhancing guest experiences.
5. Staff Dedication: The hardworking nature of the staff was recognized, contributing to the overall positive atmosphere of the hotel.

**Negative:**
1. Financial Transparency: Some guests experienced issues with overcharging and billing disputes, feeling the pricing was not transparent.
2. Restrictive Policies: There were complaints about hotel policies, such as restrictions on food delivery services and photography.
3. Service Inconsistencies: A few guests mentioned staffing challenges and unmet expectations regarding hotel services and taxi arrangements.
4. Construction Annoyances: Ongoing construction and lack of upfront information about it caused inconvenience for some guests.
5. Room Problems: Instances of room allocation issues, including being moved to an unsatisfactory room, were a source of frustration.

**Most positive examples:**
1. "amazing facility great location and value for money"
2. "best located hotel for its price"
3. "brilliant value for money trip"
4. "definitely worth it"
5. "true value for money what you pay"

**Most negative examples:**
1. "feeling fooled by the system algorithm"
2. "if i wanted to have it cleaned i wouldn't put dnd"
3. "they want you to use their taxis that charge double the rate"
4. "refused to honor their original pricing"

```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'value & values'
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
AND TRIM(Category) = 'value & values'
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

**Positive Headlines**
```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{positive_headlines}" search="true" rows=40 rowShading=true/>

### Positive Snippets
```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

### Neutral Headlines
```sql neutral_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{neutral_headlines}" search="true" rows=40 rowShading=true/>

### Neutral Snippets
```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>

### Negative Headlines
```sql negative_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```
<DataTable data="{negative_headlines}" search="true" rows=40 rowShading=true/>

### Negative Snippets
```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Snippet ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>


# Customer sentiment distribution (2022-2023)

```sql sentiment_distribution
SELECT
  TRIM(LOWER(polarity)) AS Polarity,
  Year,
  COUNT(DISTINCT review_id) AS ReviewCount
FROM
  hotels.titles
WHERE
  travel_date BETWEEN '2022-01-01' AND '2023-12-31'
  AND TRIM(LOWER(Category)) = 'value & values'
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