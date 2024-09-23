# Categories

<!-- SECTION 2: BUILD A SIMPLE CATEGORIES PAGE -->

<!-- 1. Add a new page called categories.md -->

<!-- 2. Copy and paste these instructions onto that page -->

<!-- 3. Add a query that pulls the category, request_type, and case count -->

```sql categories
select 
category,
request_type,
count(*) as cases
from sf311.cases
group by all
order by cases desc
```

<!-- 4. Add a DataTable component that includes conditional formatting for the case count column (see https://docs.evidence.dev/components/data-table/#conditional-formatting) -->

<DataTable data={categories} search=true rows=25>
    <Column id=category/>
    <Column id=request_type wrap=true/>
    <Column id=cases contentType=colorscale/>
</DataTable>

<!-- 5. Add search to the table -->

<!-- END OF SECTION 2 -->





