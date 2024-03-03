
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
    AND TRIM(Category) = 'welcome & valued'
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
    AND LOWER(TRIM(Category)) = 'welcome & valued'
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

## Positive:

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
 

## Negative:

1. Service Availability: There were isolated reports of early check-in no longer being available, causing
minor inconvenience.
2. Financial Discrepancies: A few guests encountered issues with being double charged or financial
transactions not being processed promptly.
3. Inattentive Service: Some guests noted a lack of attentiveness in the restaurant, with slow service and
orders being overlooked

<br>

## Most Positive Examples:

1. "Anjali is a superstar."
2. "Manager Dawod met us and looked after the stay."
3. "Special thanks to Qasim who let us know all the means of transportation."
4. "Gentleman Clark helped with our bags and was very polite and helpful."
5. "If you’re looking for somewhere affordable yet luxurious with excellent service, this is definitely the
place for you."

## Most Negative Examples:

1. "Concierge was hesitant to assist us during several visits."
2. "Lazy and ignored us when we didn't tip."
3. "Amount is still not in the account."
4. "Never actually doing anything to rectify the issues."
5. "Changed the availability of early check-in to no longer available.

<br>


# Headlines and corresponding snippets from reviews


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

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'welcome & valued'
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
    AND TRIM(LOWER(Category)) = 'welcome & valued' -- Change category as needed
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
        
        The feedback from guests indicates that there are several areas where the hotel's
service could be improved to make guests feel more welcome and valued. Issues
range from staff responsiveness to the efficiency of service delivery in the restaurant.
Some guests have experienced delays and a lack of attention, which has impacted
their perception of the hotel's commitment to guest satisfaction. Additionally, there
are concerns about billing accuracy and the handling of special requests, such as
early check-in. The presence of staff who appear disengaged and the inconsistent
offering of compensatory upgrades also contribute to guests' mixed feelings. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Staff responsiveness and attentiveness (e.g., &quot;concierge was hesitant to
assist us,&quot; &quot;restaurant staff who need to be more attentive and helpful&quot;).</li>
<li>Service delays (e.g., &quot;it took us almost an hour to receive our starter&quot;).</li>
<li>Perceived unfair treatment (e.g., &quot;family came and they were served ahead of
us&quot;).</li>
<li>Inattentive and disengaged staff (e.g., &quot;person wearing a green jacket who
often seemed lost and distracted,&quot; &quot;lazy and ignored us when we didn&#39;t tip&quot;).</li>
<li>Inconsistency in service offerings (e.g., &quot;offering upgrades for next visits,&quot;
&quot;stop offering upgrades for next visits&quot;).</li>
<li>Communication issues (e.g., &quot;listens to respond and doesn’t listen to
understand&quot;).</li>
<li>Failure to address problems (e.g., &quot;never actually doing anything to rectify the
issues&quot;).</li>
<li>Billing errors (e.g., &quot;double charged for our room,&quot; &quot;amount is still not in the
account&quot;).</li>
<li>Handling of special requests (e.g., &quot;changed the availability of early check in
to no longer available&quot;).</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To improve the guest experience, the hotel should focus on staff training to
ensure that all team members are proactive, attentive, and responsive to
guest needs.</li>
<li>Implementing a more rigorous service standard, particularly in the restaurant,
could help reduce wait times and ensure all guests are served promptly and
fairly.</li>
<li>Regular staff meetings to reinforce the importance of guest satisfaction and to
discuss feedback could also be beneficial. Additionally, the hotel should
review its billing processes to prevent errors and establish a clear policy on
upgrades and special requests to avoid confusion and inconsistency.</li>
<li>Finally, the hotel might consider a system to track and follow up on guest
issues to ensure they are resolved satisfactorily</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        The hotel appears to be performing well in several areas related to guest
satisfaction, particularly in the domains of customer service, housekeeping, and
personalized attention. The staff's friendliness and professionalism are frequently
mentioned, with specific employees being highlighted for their exceptional service.
The concierge, valet, and travel desk services are noted for their convenience and
helpfulness, contributing to a positive guest experience. Housekeeping receives
praise for their attention to detail and responsiveness to guest needs. The reception
team is recognized for their warm welcomes and efficient handling of guest requests,
including room upgrades and early check-ins. Overall, the hotel seems to be creating
a hospitable environment where guests feel cared for and valued. 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Staff friendliness and professionalism (e.g., &quot;very friendly staff,&quot; &quot;professional
service was excellent&quot;)</li>
<li>Personalized attention and recognition of guest needs (e.g., &quot;always checking
on me to make sure I have everything I need,&quot; &quot;remembered my preference
as well as my family&#39;s&quot;)</li>
<li>Efficient and helpful concierge service (e.g., &quot;concierge was great,&quot; &quot;concierge
team daiwood, emad, zahra, kim, april&quot;)</li>
<li>Responsive and thorough housekeeping (e.g., &quot;professional cleaning team,&quot;
&quot;housekeeping was amazing&quot;)</li>
<li>Reception team&#39;s warm welcomes and efficiency (e.g., &quot;reception staff
extremely friendly and helpful,&quot; &quot;reception upgraded my booking&quot;)</li>
<li>Valet and travel desk services adding convenience (e.g., &quot;valet service was
efficient,&quot; &quot;agent quasim butt from travel desk gave us&quot;)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To further enhance guest satisfaction, the hotel could consider implementing a
guest recognition program to acknowledge returning guests and those who
provide positive feedback. Additionally, ensuring that all staff members are
equally recognized and trained to maintain the high standards of service could
help sustain the positive guest experiences.</li>
<li>Regular staff training sessions focused on customer service excellence and
problem-solving could also be beneficial.</li>
<li>Lastly, gathering and acting on guest feedback continuously can help identify
areas for improvement and reinforce the hotel&#39;s commitment to guest
satisfaction.</li>


    </ol> 
    </Tab>
</Tabs>