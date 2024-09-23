

<!-- SECTION 4: BUILD A NEIGHBORHOOD EXPLORER PAGE -->

  <!-- 1. Add a folder called neighborhoods in your pages directory. Then add a file called index.md in that folder -->

  <!-- 2. Copy and paste these instructions onto that page -->

  <!-- 3. Add a page title -->

  <!-- 4. Build a neighborhood map and make it an interactive input
          - Start with a SQL query that gets neighborhood and case count - call the query 'neighborhoods'
          - Add an AreaMap component pulling from that data
          - Make the map an input using the 'name' prop - give it a name of 'map_input'
  -->

  <!-- 5. Set up a page title that changes to show the selected neighborhood name
        - When not selected, the input will default to true
        - You will need to use an if block (https://docs.evidence.dev/core-concepts/if-else/)
        - Title should say "All Neighborhoods" when no neighborhood is selected
  -->

  <!-- 6. Set up a query to get the case trend for the selected neighborhood. 
          - Name the query 'filtered_trend'
          - Get the week and case count for that specific neighborhood using a where clause
          - See here for an example: https://docs.evidence.dev/components/area-map/#use-map-as-input
          - Note that you will need to handle the situation where no neighborhood is selected (the input will return true in that case)
  -->

  <!-- 7. Add a LineChart to display the trend data from filtered_trend -->

  <!-- 8. Put your AreaMap and LineChart into a Grid with 2 columns
          - You can also give each a title by adding a header above them (e.g., put '### Neighborhood Selector' above the AreaMap)
              - To avoid creating new Grid cells when appending content to a cell, you can use the Group component (https://docs.evidence.dev/components/grid/#group-component)
  -->

  <!-- 9. Add a category breakdown
          - Create a query called category_breakdown which pulls the category and case count, filtered for the selected neighborhoood
          - Add a DataTable to display the results and use conditional formatting for the case count column
  -->

<!-- END OF SECTION 4 -->

##### Neighborhood Explorer

{#if inputs.map_input.neighborhood === true}

    # All Neighborhoods

{:else}

    # {inputs.map_input.neighborhood}

{/if}

```sql neighborhoods
select neighborhood, count(*) as cases
from sf311.cases
where neighborhood is not null
group by all
order by cases desc
```

```sql filtered_trend
select date_trunc('week', opened) as date,
count(*) as cases
from sf311.cases
where opened <= '2024-08-31'
and (neighborhood = '${inputs.map_input.neighborhood}'
or '${inputs.map_input.neighborhood}' = 'true')
group by all
order by date
```

<Grid cols=2>
    <Group>

        ### Neighborhood Selector

        <AreaMap
            data={neighborhoods}
            geoJsonUrl='https://evd-geojson.b-cdn.net/sf_hoods.geojson'
            geoId=name
            areaCol=neighborhood
            value=cases
            name=map_input
        />

    </Group>
    <Group>

        ### Weekly Case Trend

        <LineChart
            data={filtered_trend}
            x=date
            y=cases
            chartAreaHeight=280
        />

    </Group>
</Grid>

### Category Breakdown

```sql category_breakdown
select
    category,
    count(*) as cases
from sf311.cases
where neighborhood = '${inputs.map_input.neighborhood}'
or '${inputs.map_input.neighborhood}' = 'true'
group by all
order by cases desc
```

<DataTable data={category_breakdown}>
    <Column id=category/>
    <Column id=cases contentType=colorscale/>
</DataTable>