

**Summary**

Overall sentiment regarding care, respect, and being valued in the hotel leans heavily towards the positive, with approximately 98.12% of guests expressing satisfaction. A total of 994 guests left positive reviews, while only 10 guests reported negative experiences.


**Positive:**

1. Exceptional Service: Guests praised the attentive and personalized service, with staff like Anjali and
Masud receiving special mentions for their dedication.
2. Accommodating Requests: Many were impressed by the hotel's willingness to fulfill special requests,
such as room upgrades and early check-ins, enhancing their stay.
3. Staff Recognition: The efforts of housekeeping teams, valets, and receptionists were frequently
acknowledged, highlighting their politeness and helpfulness.
4. Hospitality Excellence: The overall hospitality, including management engagement and staff
performance, was celebrated for creating memorable experiences.
5. Tour Assistance: Guests appreciated the guidance and efficient organization of tours and activities,
with staff like Qasim Butt providing excellent recommendations.
 

**Negative:**

1. Service Availability: There were isolated reports of early check-in no longer being available, causing
minor inconvenience.
2. Financial Discrepancies: A few guests encountered issues with being double charged or financial
transactions not being processed promptly.
3. Inattentive Service: Some guests noted a lack of attentiveness in the restaurant, with slow service and
orders being overlooked

<br>

**Most Positive Examples:**

1. "Anjali is a superstar."
2. "Manager Dawod met us and looked after the stay."
3. "Special thanks to Qasim who let us know all the means of transportation."
4. "Gentleman Clark helped with our bags and was very polite and helpful."
5. "If you’re looking for somewhere affordable yet luxurious with excellent service, this is definitely the
place for you."

**Most Negative Examples:**

1. "Concierge was hesitant to assist us during several visits."
2. "Lazy and ignored us when we didn't tip."
3. "Amount is still not in the account."
4. "Never actually doing anything to rectify the issues."
5. "Changed the availability of early check-in to no longer available.

<br>

```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'welcome & valued'
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
AND TRIM(Category) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
  AND TRIM(LOWER(Category)) = 'welcome & valued'
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