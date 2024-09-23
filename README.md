# SF 311 Data App Template

This is a template repo for building an interactive data app with [Evidence](https://evidence.dev) using SF 311 data. This project is part of a workshop at [Small Data SF 2024](https://www.smalldatasf.com/2024/).

Please read this full readme before starting.

## Getting Started

We recommend using VS Code and installing the Evidence extension.

You can get started by clicking the "Use this template" button at the top right of the page and creating a new repository. **Make sure you check the box to include all branches.**

You will see some banners in Github prompting you to open a PR for the other branches - ignore these.

Once you have the repo cloned locally, check out the `pages/index.md` file for instructions on what to build next.

## Template Contents

This template repo includes a DuckDB dataset containing 100k SF 311 records, as well as all required connection setup in Evidence. You don't need to do anything to connect the data source to the Evidence project.

## Important Note: geoJSON File

We have hosted a geoJSON file for mapping SF neighborhoods. You will need the URL for this file when creating an AreaMap:
https://evd-geojson.b-cdn.net/sf_hoods.geojson

## Sections

We've broken up the proejct into 6 sections:

1. Build a basic summary page
2. Build a simple categories page
3. Add interactivity to your summary page
4. Build a neighborhood explorer page
5. Create neighborhood templated page
6. Dynamic content generation - volume spikes

At the end of the project, you'll have a fast, fully interactive data app that looks great on desktop, laptop, or mobile devices.

## Branches

You should get started on the `main` branch of this repo.

We have created progress branches - one branch per section - which include the completed content for that section. 

At any time if you get stuck and need to jump ahead to catch up, you can check out one of those branches. Branches are named `section-1` through `section-6`.

You will need to either commit or discard your changes when switching branches, and then can switch like so:

```
git checkout section-1
```

## Resources

- Docs: https://docs.evidence.dev
- Finished Project Repo: https://github.com/hughess/sf311
- Deployed Finished Project: https://sf311.evidence.app/

## Using Codespaces

If you are using this template in Codespaces, click the `Start Evidence` button in the bottom status bar. This will install dependencies and open a preview of your project in your browser - you should get a popup prompting you to open in browser.

Or you can use the following commands to get started:

```bash
npm install
npm run sources
npm run dev -- --host 0.0.0.0
```

See [the CLI docs](https://docs.evidence.dev/cli/) for more command information.

**Note:** Codespaces is much faster on the Desktop app. After the Codespace has booted, select the hamburger menu → Open in VS Code Desktop.


## Learning More

- [Docs](https://docs.evidence.dev/)
- [Github](https://github.com/evidence-dev/evidence)
- [Slack Community](https://slack.evidence.dev/)
- [Evidence Home Page](https://www.evidence.dev)


## Full Instructions

### SECTION 1: BUILD A BASIC SUMMARY PAGE

1. **Add your first query**
   - Name it `description`
   - Calculate `min_date`, `max_date`, and case count from the `sf311.cases` table

2. **Add a Details component to explain the dataset**
   - Use the `Value` component to display the min date, max date, and record count in the `Details` component
   - Add a nested `Details` component inside to display this explanation of 311 cases:
     > 311 is the city's request service for non-emergencies. Citizens can report local issues to 311 and monitor the status of the request. Each case represents something that was reported through this service.

3. **Add summary BigValues**
   - Add a query called `summary` to get `total_cases` and `open_cases` (where `status='Open'`)
   - Add a `BigValue` for each of those metrics

4. **Add a trend line chart**
   - Add a query called `trend` which includes date and case count
   - Use the `opened` column for the date. Since this is a timestamp, you will need to use `date_trunc()`

5. **Add a DimensionGrid**
   - Create a query called `cases` which pulls these columns:
     - `responsible_agency`
     - `category`
     - `request_type`
     - `source`
   - Add a `DimensionGrid` component which uses that data

6. **Add a CalendarHeatmap** using your `trend` query

---

### SECTION 2: BUILD A SIMPLE CATEGORIES PAGE

1. Add a new page called `categories.md`
2. Copy and paste these instructions onto that page
3. Add a query that pulls the `category`, `request_type`, and case count
4. Add a `DataTable` component that includes conditional formatting for the case count column (see [conditional formatting](https://docs.evidence.dev/components/data-table/#conditional-formatting))
5. Add search to the table

---

### SECTION 3: ADD INTERACTIVITY TO YOUR SUMMARY PAGE (INDEX.MD)

1. Change your `DimensionGrid` to become an input by using the `name` prop - call it `dimensions`
2. Hook this up to the `LineChart` by adding a `where` clause to your trend query. See docs here: [DimensionGrid as an Input](https://docs.evidence.dev/components/dimension-grid/#as-an-input)
3. Add the input to the chart title so you get the context when the chart updates. Inputs can be referenced in curly braces like: `{inputs.my_input}`

---

### SECTION 4: BUILD A NEIGHBORHOOD EXPLORER PAGE

1. Add a folder called `neighborhoods` in your `pages` directory. Then add a file called `index.md` in that folder.
2. Copy and paste these instructions onto that page.
3. Add a page title.
4. Build a neighborhood map and make it an interactive input:
   - Start with a SQL query that gets neighborhood and case count - call the query `neighborhoods`
   - Add an `AreaMap` component pulling from that data
   - Make the map an input using the `name` prop - give it a name of `map_input`
5. Set up a page title that changes to show the selected neighborhood name:
   - When not selected, the input will default to true
   - You will need to use an if block ([If-Else Documentation](https://docs.evidence.dev/core-concepts/if-else/))
   - Title should say "All Neighborhoods" when no neighborhood is selected
6. Set up a query to get the case trend for the selected neighborhood:
   - Name the query `filtered_trend`
   - Get the week and case count for that specific neighborhood using a `where` clause
   - Example: [Use Map as Input](https://docs.evidence.dev/components/area-map/#use-map-as-input)
7. Add a `LineChart` to display the trend data from `filtered_trend`
8. Put your `AreaMap` and `LineChart` into a Grid with 2 columns:
   - Add a header above each component (e.g., `### Neighborhood Selector` above the `AreaMap`)
   - Use the `Group` component to avoid creating new grid cells (see [Group Documentation](https://docs.evidence.dev/components/grid/#group-component))
9. Add a category breakdown:
   - Create a query called `category_breakdown` which pulls the category and case count, filtered for the selected neighborhood
   - Add a `DataTable` to display the results and use conditional formatting for the case count column

---

### SECTION 5: CREATE NEIGHBORHOOD TEMPLATED PAGE

1. **Create links Evidence will use to auto-generate templated pages**
   - In `pages/neighborhoods/index.md`, add a `DataTable` at the bottom of the page pulling from the `neighborhoods` query
   - Use the `link` prop in `DataTable` to set the link to the `neighborhood` column
2. Create a new file called `[neighborhood].md` inside your neighborhoods folder
3. Copy and paste these directions onto that page
4. Add a title for the neighborhood using the "page param" ([Templated Pages Documentation](https://docs.evidence.dev/core-concepts/templated-pages/))
5. Test by clicking a row in your `DataTable` to navigate to a neighborhood page
6. Add a summary `BigValue`:
   - Add a query called `summary` which pulls the case count and close rate (`cases where status=closed / total cases`)
   - Add a `BigValue` component to display the count and close rate (use the comparison for the close rate - [Comparisons Documentation](https://docs.evidence.dev/components/big-value/#comparisons))
7. Get the last 100 cases in that neighborhood:
   - Call this query `last_100`
   - Filter for the neighborhood using the page param
8. Add a `PointMap` to display the last 100 cases using the latitude and longitude columns:
   - Try using the URL column in the tooltip to create a link to the SF city site (see [Clickable Link and Tooltip Documentation](https://docs.evidence.dev/components/point-map/#with-clickable-link-and-tooltiptypeclick))
9. Pull the top 10 categories for the neighborhood:
   - Create a query called `top_categories`
   - Display the results in a horizontal `BarChart` (use `swapXY=true`)
10. Create a 2-column Grid and put your `PointMap` and `BarChart` inside it:
    - Adjust the `BarChart` height using `chartAreaHeight` to align with the `PointMap`
11. Create a dynamic link in your neighborhood explorer by adding this to the bottom of your page title:
    - `[See neighborhood deep dive →](./{inputs.map_input.neighborhood})`

---

### SECTION 6: DYNAMIC CONTENT GENERATION - VOLUME SPIKES

1. **Copy and paste these directions into your `[neighborhood].md` file**
2. Add a query to detect volume spikes in cases over time:
   - Call the query `spike_detection`
   - Use this SQL:

```sql
   WITH case_counts AS (
       SELECT category,
              date_trunc('week', opened) AS date,
              COUNT(1) AS cases
       FROM sf311.cases
       WHERE neighborhood = '${params.neighborhood}'
       GROUP BY ALL
   ),
   
   category_stats AS (
       SELECT category,
              date,
              cases,
              AVG(cases) OVER (PARTITION BY category ORDER BY date ROWS BETWEEN 30 PRECEDING AND CURRENT ROW) AS rolling_avg,
              STDDEV(cases) OVER (PARTITION BY category ORDER BY date ROWS BETWEEN 30 PRECEDING AND CURRENT ROW) AS rolling_stddev
       FROM case_counts
   )
   
   SELECT category,
          date,
          cases,
          rolling_avg,
          rolling_stddev,
          (cases - rolling_avg) / rolling_stddev AS stddev,
          CASE WHEN cases > (rolling_avg + 2 * rolling_stddev) THEN 'Spike'
               ELSE 'Normal'
          END AS status
   FROM category_stats;
```


3. Add another query called all_spikes to filter for spikes using the status column (status = 'Spike')

4. Add a section with a title for Volume Spikes in this neighborhood:
    - Include a Details component with this definition:
        > A volume spike is defined as a weekly case count exceeding 2x the standard deviation of the previous 30 weeks.

5. Pull the distinct list of categories with spikes in a query called categories_with_spikes:
    - Sum the cases from the all_spikes query
    - Use [query chaining](https://docs.evidence.dev/core-concepts/queries/#query-chaining) to accomplish this

6. Add an if block to display different content depending on whether spikes are present:
    - Use categories_with_spikes.length in the if block to check if there are results
    - Display text confirming whether there are spikes (e.g., "There are no spikes above 2 standard deviations from the 30-week rolling average")

7. Include a loop of line charts to display spikes:
    - Inside the if block, add a Grid with 3 columns
    - Inside the Grid, add a loop using an #each block ([Loop Documentation](https://docs.evidence.dev/core-concepts/loops/))
    - Add a query that filters the spike_detection query to show results only for the current category in the loop
    - Use a line chart to display those results
