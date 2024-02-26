---
title: Impression, 2022-2023
---

# Summary

#### Overall, the general impressions from hotel reviews reveal a predominantly positive sentiment, with guests expressing high levels of satisfaction, excitement, and intent to return. Approximately 80.9% of the feedback is positive, with 4.6% expressing some negative experiences and 17.6% expressed neutral expereiences. Positive reviews: 672; Negative reviews: 34; Neutral reviews: 152

 

#### Positive:

1. Exceptional Service: Guests praised the staff and services, with special mentions of the Avani app and
the superb service in Dubai.
2. Dining Delights: The Seven Seeds restaurant received accolades for its lovely iftar and memorable
dining experiences.
3. High Recommendations: Many guests rated their experience 10/10, with strong endorsements for the
hotel's service, facilities, and overall stay.
4. Loyal Guests: Repeat visitors described their stays as memorable, with one guest staying for over two
years and still counting.
5. General Satisfaction: Guests consistently mentioned perfect stays, amazing experiences, and the hotel
meeting all expectations.
 

#### Negative:

1. Service Improvement: A few guests suggested improvements in service speed and quality.
2. Health and Safety: There were concerns about lost items and the well-being of guests.
3. Room Issues: Some guests faced issues with room reassignments and inadequate room services.
4. Transportation Concerns: The hotel's drop-off area was noted to be small and often crowded.
5. Negative Experiences: A small number of guests had negative experiences, with one stating that Avani
"ruined it" for them.

#### Most Positive Examples:

1. "Avani hotel one of my best accommodations in Dubai."
2. "Maheender and Meena did a great job, thanks a lot for these beautiful days."
3. "Perfect place to spend holidays."
4. "Spent 3 remarkable days."
5. "They were just amazing."

 

#### Most Negative Examples:

1. "Very bad."
2. "Avani ruined it that bad for us."
3. "Make the stay there terrible."
4. "Worst experiences."
5. "I do not recommend this place."


```sql polarity_proportions
WITH CategoryCounts AS (
    SELECT
        TRIM(polarity) AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'impression'
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
AND TRIM(Category) = 'impression'
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





# Snippets from reviews

## Positive Snippets
```sql positive_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impression'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Headline ASC
```

<DataTable data="{positive_snippets}" search="true" rows=15 rowShading=true/>

## Neutral Snippets

```sql neutral_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impression'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Headline ASC
```

<DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>

## Negative Snippets

```sql negative_snippets
SELECT Headline, Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impression'
AND (polarity = 'negative' OR polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
ORDER BY Headline ASC
```

<DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>


## Customer sentiment distribution (2022-2023)

```sql sentiment_distribution
SELECT
  TRIM(LOWER(polarity)) AS Polarity,
  Year,
  COUNT(DISTINCT review_id) AS ReviewCount
FROM
  hotels.titles
WHERE
  travel_date BETWEEN '2022-01-01' AND '2023-12-31'
  AND TRIM(LOWER(Category)) = 'impression'
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