
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
