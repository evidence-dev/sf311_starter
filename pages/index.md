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


