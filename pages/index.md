---
title: San Francisco 311 Cases
---

<!-- SECTION 1: BUILD A BASIC SUMMARY PAGE -->

<!-- 1. Add your first query
    - Name it 'description'
    - Calculate min_date, max_date, and case count from the sf311.cases table
-->

```sql description
select min(opened) as min_date, max(opened) as max_date, count(*) as count
from sf311.cases
```

<!-- 2. Add a Details component to explain the dataset
    - Use the Value component to display the min date, max date, and record count in the Details componet
    - Add a nested Details component inside to display this explanation of 311 cases:
          311 is the city's request service for non-emergencies. Citizens can report local issues to 311 and monitor the status of the request. Each case represents something that was reported through this service.
--> 

<Details title='About this data'>
  This dataset includes <Value data={description} column=count fmt=num0k/> 311 cases in San Francisco from <Value data={description} column=min_date fmt=shortdate/> to <Value data={description} column=max_date fmt=shortdate/>.

  <Details title='What are 311 Cases?'>
    311 is the city's request service for non-emergencies. Citizens can report local issues to 311 and monitor the status of the request. Each case represents something that was reported through this service.
  </Details>

</Details>

<!-- 3. Add summary BigValues
    - Add a query called 'summary' to get total_cases and open_cases (where status='Open')
    - Add a BigValue for each of those metrics
-->

```sql summary
select count(*) as total_cases, count(*) filter (status = 'Open') as open_cases
from sf311.cases
```

<BigValue
  data={summary}
  value=total_cases
  fmt=num0k
/>

<BigValue
  data={summary}
  value=open_cases
  fmt=num0k
/>

<!-- 4. Add a trend line chart
    - Add a query called 'trend' which includes date and case count
    - Use the opened column for the date. Since this is a timestamp you will need to use date_trunc()
-->

```sql trend
select date_trunc('day', opened) as date,
count(*) as cases
from sf311.cases
where ${inputs.dimensions}
group by all
order by date asc
```

<LineChart
  title="Case Trend"
  data={trend}
  x=date
  y=cases
  yAxisTitle=cases
/>

<!-- 5. Add a DimensionGrid 
  - Create a query called 'cases' which pulls these columns:
      - responsible_agency
      - category
      - request_type
      - source
  - Add a DimensionGrid component which uses that data
-->

```sql cases
select 
  responsible_agency,
  category,
  request_type,
  source
from sf311.cases 
```

<DimensionGrid 
  data={cases}
  name=dimensions
/>

<!-- 7. Add a CalendarHeatmap which uses your 'trend' query -->

<CalendarHeatmap
  title="Daily 311 Cases"
  data={trend}
  date=date
  value=cases
/>

<!-- END OF SECTION 1 -->



<!-- SECTION 3: ADD INTERACTIVITY TO YOUR SUMMARY PAGE (INDEX.MD) -->

<!-- 1. Change your dimension grid to become an input by using the 'name' prop - call it 'dimensions' -->

<!-- 2. Then hook this up to the LineChart by adding a 'where' clause to your trend query. See docs here: https://docs.evidence.dev/components/dimension-grid/#as-an-input -->

<!-- END OF SECTION 3 -->



<!-- SECTION 6: DYNAMIC CONTENT GENERATION - VOLUME SPIKES -->

  <!-- 1. Copy and paste these directions into your [neighborhood].md file -->


  <!-- 2. Add a query to detect volume spikes in cases over time
      - Call the query 'spike_detection'
      - Use this SQL:
  
  WITH case_counts as (
      select 
          category,
          date_trunc('week', opened) as date,
          count(1) as cases
      from sf311.cases
      where neighborhood = '${params.neighborhood}'
      group by all
  ),

  category_stats AS (
      SELECT 
          category,
          date,
          cases,
          AVG(cases) OVER (PARTITION BY category ORDER BY date ROWS BETWEEN 30 PRECEDING AND CURRENT ROW) AS rolling_avg,
          STDDEV(cases) OVER (PARTITION BY category ORDER BY date ROWS BETWEEN 30 PRECEDING AND CURRENT ROW) AS rolling_stddev
      FROM 
          case_counts
  )

  SELECT 
      category,
      date,
      cases,
      rolling_avg,
      rolling_stddev,
      (cases - rolling_avg) / rolling_stddev as stddev,
      CASE 
          WHEN cases > (rolling_avg + 2 * rolling_stddev) THEN 'Spike'
          ELSE 'Normal'
      END AS status
  FROM 
      category_stats

  -->

  <!-- 3. Add another query called all_spikes to filter for spikes using the status column (status = 'Spike') -->

  <!-- 4. Add a section with a title for Volume Spikes in this neighborhood
          - Include a Details component with this definition:
              A volume spike is defined as a weekly case count exceeding 2x the standard deviation of the previous 30 weeks
  -->

  <!-- 5. Pull the distinct list of categories with spikes in a query called 'categories_with_spikes'
          - Do this by summing the cases from the all_spikes query
          - Use query chaining to accomplish this (https://docs.evidence.dev/core-concepts/queries/#query-chaining)
  -->


  <!-- 6. Add an if block to display different content depending on whether spikes are present
          - You can use categories_with_spikes.length in the if block to determine if it has any results
          - For now, just put a simple text message to confirm that there are or aren't any spikes. In the next step we will add the real content
          - For the case where there are no spikes, you can include text like "There are no spikes above 2 standard deviations from the 30 week rolling average"
  -->

  <!-- 7. Include a loop of line charts to display spikes
          - In the part of the if block where spikes are present, add a Grid with 3 columns
          - Inside the Grid, add a loop using an #each block (https://docs.evidence.dev/core-concepts/loops/)
          - The each block should use the categories_with_spikes query
          - Add a LineChart inside the loop - use the below for your data prop:
                  data={spike_detection.where(`category = '${row.category}'`)}
          - Test your page - you should see multiple charts appearing for neighborhoods that have spikes
  -->

  <!-- 8. Add annotations to show spikes on the line charts
          - Inside the LineChart in your loop, add a ReferencePoint component (https://docs.evidence.dev/components/annotations/#reference-point)
          - Use this code for the ReferencePoint:
                      <ReferencePoint
                          data={all_spikes.where(`category = '${row.category}'`)}
                          x=date
                          y=cases
                          label=status
                          color=red
                          symbolSize=16
                          symbolBorderWidth=1
                          symbolBorderColor=red
                          symbolOpacity=0.25
                      />
          - Check out your page again to see the spikes circled in red
  -->

  <!-- 9. That's it! You now have a fully interactive data app running locally. Nice work! -->

<!-- END OF SECTION 6 -->
