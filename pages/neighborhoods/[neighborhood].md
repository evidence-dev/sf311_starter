
<!-- SECTION 5: CREATE NEIGHBORHOOD TEMPLATED PAGE -->

  <!-- 1. Create links Evidence will use to auto-generate your templated pages when deploying
      - In pages/neighborhoods/index.md, add a DataTable to the bottom of the page pulling from the 'neighborhoods' query
      - Use the 'link' prop in DataTable to set the link to the 'neighborhood' column
      - This will make each row in the table clickable and will tell Evidence to generate a page for that neighborhood when building your project for deployment
  -->

  <!-- 2. Create a new file called [neighborhood].md inside your neighborhoods folder -->

  <!-- 3. Copy and paste these directions onto that page -->

  <!-- 4. Add a title for the neighborhood using the "page param" (see here: https://docs.evidence.dev/core-concepts/templated-pages/) -->

  <!-- 5. Go back to your neighborhood explorer page and test by clicking a row in your DataTable you created in step 1. You should navigate to a neighborhood page and see the title you just added with the neighborhood name -->

  <!-- 6. Add a summary BigValue 
          - Add a query called summary which pulls the case count and close rate (cases where status=closed / total cases)
          - Add a BigValue component to display the count and close rate (use the comparison for the close rate - https://docs.evidence.dev/components/big-value/#comparisons)
  -->
  
  <!-- 7. Get the last 100 cases in that neighborhood
          - Call this query 'last_100'
          - Filter for the neighborhood using the page param in your query
  -->

  <!-- 8. Add a PointMap to display the last 100 cases using the latitude and longitude columns 
      - See if you can use the url column in the tooltip to create a link to the SF city site
      - Docs: https://docs.evidence.dev/components/point-map/#with-clickable-link-and-tooltiptypeclick
  -->

  <!-- 9. Pull the top 10 categories for the neighborhood 
          - Create a query called top_categories
          - Display the results in a horizontal BarChart (use swapXY=true)
  -->

  <!-- 10. Create a 2-column Grid and put your PointMap and BarChart inside it
          - Adjust the BarChart height using chartAreaHeight to get it to line up well with the PointMap
  -->

  <!-- 11. Create a dynamic link in your neighborhood explorer by adding this to the bottom of your page title:
      -  [See neighborhood deep dive &rarr;](./{inputs.map_input.neighborhood})
      - You should now see a deep dive link appear when you click on a neighborhood on the map
  -->

<!-- END OF SECTION 5 -->

# {params.neighborhood}

```sql summary
with case_summary as (
    select count(*) as count,
    count(*) filter (status = 'Closed') as closed
    from sf311.cases
    where neighborhood = '${params.neighborhood}'
)

select *, closed / count as close_rate
from case_summary
```

<BigValue
    data={summary}
    value=count
    fmt=num0
    title="Cases YTD"
    comparison=close_rate
    comparisonDelta=false
    comparisonFmt=pct
/>

```sql last_100
select * from sf311.cases
where neighborhood = '${params.neighborhood}'
and latitude <> 0
order by opened desc
limit 100
```

```sql top_categories
select category, count(*) as cases
from sf311.cases
    where neighborhood = '${params.neighborhood}'
group by all
order by cases desc
limit 10
```

<Grid cols=2>
    <PointMap
        title="Last 100 Cases"
        data={last_100}
        lat=latitude
        long=longitude
        startingZoom=13
        name=selected_point
        tooltipType=click
        tooltip={[
            {id: 'category', showColumnName: false, valueClass: 'font-bold text-lg'},
            {id: 'status'},
            {id: 'url', showColumnName: false, contentType: 'link', linkLabel: 'SF City Record &rarr;', valueClass: 'font-bold mt-1'}
        ]}
    />

    <BarChart
        title="Top 10 Categories"
        data={top_categories}
        x=category
        y=cases
        swapXY=true
        chartAreaHeight=200
    />
    
</Grid>



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


```sql spike_detection
WITH case_counts as (
    select 
        category,
        date_trunc('week', opened) as date,
        count(*) as cases
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
```

```sql all_spikes
select *
from ${spike_detection}
where status = 'Spike'
```

# Weekly Volume Spikes in {params.neighborhood}

<Details title='What is a volume spike?'>
    A volume spike is defined as a weekly case count exceeding 2x the standard deviation of the previous 30 weeks
</Details>


```sql categories_with_spikes
select category, sum(cases) as cases 
from ${all_spikes}
group by all
order by cases desc
```

{#if categories_with_spikes.length > 0}

<Grid cols=3>

{#each categories_with_spikes as row}

    <LineChart
        title={row.category}
        data={spike_detection.where(`category = '${row.category}'`)}
        x=date
        y=cases
        handleMissing=zero
    >
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
    </LineChart>

{/each}

</Grid>

{:else}

There are no spikes above 2 standard deviations from the 30 week rolling average

{/if}


