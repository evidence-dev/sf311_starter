---
title: San Francisco 311 Cases
---

<!-- SECTION 1: BUILD A BASIC SUMMARY PAGE -->

<!-- 1. Add your first query
    - Name it 'description'
    - Calculate min_date, max_date, and case count from the sf311.cases table
-->

<!-- 2. Add a Details component to explain the dataset
    - Use the Value component to display the min date, max date, and record count in the Details componet
    - Add a nested Details component inside to display this explanation of 311 cases:
          311 is the city's request service for non-emergencies. Citizens can report local issues to 311 and monitor the status of the request. Each case represents something that was reported through this service.
--> 

<!-- 3. Add summary BigValues
    - Add a query called 'summary' to get total_cases and open_cases (where status='Open')
    - Add a BigValue for each of those metrics
-->

<!-- 4. Add a trend line chart
    - Add a query called 'trend' which includes date and case count
    - Use the opened column for the date. Since this is a timestamp you will need to use date_trunc()
-->

<!-- 5. Add a DimensionGrid 
  - Create a query called 'cases' which pulls these columns:
      - responsible_agency
      - category
      - request_type
      - source
  - Add a DimensionGrid component which uses that data
-->

<!-- 7. Add a CalendarHeatmap which uses your 'trend' query -->

<!-- END OF SECTION 1 -->




<!-- SECTION 2: BUILD A SIMPLE CATEGORIES PAGE -->

<!-- 1. Add a new page called categories.md -->

<!-- 2. Copy and paste these instructions onto that page -->

<!-- 3. Add a query that pulls the category, request_type, and case count -->

<!-- 4. Add a DataTable component that includes conditional formatting for the case count column (see https://docs.evidence.dev/components/data-table/#conditional-formatting) -->

<!-- 5. Add search to the table -->

<!-- END OF SECTION 2 -->



<!-- SECTION 3: ADD INTERACTIVITY TO YOUR SUMMARY PAGE (INDEX.MD) -->

<!-- 1. Change your dimension grid to become an input by using the 'name' prop - call it 'dimensions' -->

<!-- 2. Then hook this up to the LineChart by adding a 'where' clause to your trend query. See docs here: https://docs.evidence.dev/components/dimension-grid/#as-an-input -->

<!-- 3. Add the input to the chart title so you get the context when the chart updates. Inputs can be referenced in curly braces like: {inputs.my_input} -->

<!-- END OF SECTION 3 -->



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
