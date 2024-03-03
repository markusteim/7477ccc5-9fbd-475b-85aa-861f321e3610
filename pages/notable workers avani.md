## We've pinpointed a group of standout employees whose recognition could serve to enhance your team's morale and performance


<Tabs>
    <Tab label="Exemplary Workers">
    <Tabs>
    <Tab label="Employee Names">
        <DataTable data="{positive_employees}" search="true" rows=20 rowShading=true/>
    </Tab>
    <Tab label="Snippets">
        <DataTable data="{employee_snippets}" search="true" rows=20 rowShading=true/>
    </Tab>
    </Tabs>
    </Tab>
</Tabs>



 ```sql positive_employees
SELECT
    LOWER(n.name) AS name,
    COUNT(t.snippet) AS occurrence_count
FROM
    hotels.names_positive n
LEFT JOIN hotels.titles t ON LOWER(t.snippet) LIKE '%' || LOWER(n.name) || '%'
WHERE
    (t.Year = '2022' OR t.Year = '2023')
    AND (t.polarity = 'positive' OR t.polarity = 'very positive')
GROUP BY
    LOWER(n.name)
ORDER BY
    occurrence_count DESC
 ```

 ```sql employee_snippets
WITH EmployeeOccurrences AS (
    SELECT
        LOWER(n.name) AS name,
        COUNT(t.snippet) AS occurrence_count
    FROM
        hotels.names_positive n
    LEFT JOIN hotels.titles t ON LOWER(t.snippet) LIKE '%' || LOWER(n.name) || '%'
    WHERE
        (t.Year = '2022' OR t.Year = '2023')
        AND (t.polarity = 'positive' OR t.polarity = 'very positive')
    GROUP BY
        LOWER(n.name)
)

SELECT
    t.snippet,
    eo.name,
    eo.occurrence_count
FROM
    hotels.titles t
JOIN EmployeeOccurrences eo ON LOWER(t.snippet) LIKE '%' || eo.name || '%'
ORDER BY
    eo.occurrence_count DESC, eo.name, t.snippet
 ```