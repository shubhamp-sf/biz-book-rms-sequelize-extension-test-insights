# Relations

## @belongsTo

<details>
  <summary>GET /deals (include related project)</summary>

### Relation

```ts
@belongsTo(
  () => Project,
  {name: 'project', keyTo: 'projectId'},
  {name: 'project_id', type: 'string', required: true},
)
projectId: string;
```

### Filter 

```json
{
   "limit":1,
   "order":"id",
   "include":[
      {
         "relation":"project"
      }
   ]
}
```

### Query

```sql
SELECT 
  "deals"."id", 
  "deals"."name", 
  /* ... other `deals` table columns ... */
  "project"."id" AS "project.id", 
  "project"."name" AS "project.name", 
  /* ... other `projects` table columns ... */
FROM 
  "main"."deals" AS "deals" 
  LEFT OUTER JOIN "main"."projects" AS "project" ON "deals"."project_id" = "project"."id" 
WHERE 
  "deals"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
ORDER BY 
  "deals"."id" ASC 
LIMIT 1;

```

</details>


<details>
  <summary>GET /deals (include related project and dealStage)</summary>

### Relation

```ts
@belongsTo(
  () => Project,
  {name: 'project', keyTo: 'projectId'},
  {name: 'project_id', type: 'string', required: true},
)
projectId: string;

...

@belongsTo(() => DealStage, {keyTo: 'stageId'}, {name: 'stage_id'})
dealStageId: string;
```

### Filter 

```json
{
   "limit": 1,
   "order": "id",
   "include":[
      {
         "relation": "project"
      },
      {
         "relation": "dealStage"
      }
   ]
}
```

### Query

```sql
SELECT 
  "deals"."id", 
  "deals"."name", 
  /* ... other `deals` table columns ... */

  "project"."id" AS "project.id", 
  "project"."name" AS "project.name", 
  /* ... other `projects` table columns ... */

  "dealStage"."id" AS "dealStage.id", 
  "dealStage"."key" AS "dealStage.key", 
  /* ... other `deal_stages` table columns ... */
FROM 
  "main"."deals" AS "deals" 
  LEFT OUTER JOIN "main"."projects" AS "project" ON "deals"."project_id" = "project"."id" 
  LEFT OUTER JOIN "main"."deal_stages" AS "dealStage" ON "deals"."stage_id" = "dealStage"."id" 
WHERE 
  "deals"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
ORDER BY 
  "deals"."id" ASC 
LIMIT 1;

```
</details>


<details>
  <summary>GET /deal-views (include related dealStage)</summary>

### Relation

```ts
@belongsTo(() => DealStage, {keyTo: 'stageId'}, {name: 'stage_id'})
dealStageId: string;
```

### Filter 

```json
{
   "limit": 1,
   "include":[
      {
         "relation": "dealStage"
      }
   ]
}
```

### Query

```sql
SELECT 
  "v_deal_view"."id", 
  "v_deal_view"."name", 
  /* ... other `v_deal_view` columns ... */
  
  "dealStage"."id" AS "dealStage.id", 
  "dealStage"."key" AS "dealStage.key", 
  /* ... other `deal_stages` table columns ... */
FROM 
  "main"."v_deal_view" AS "v_deal_view" 
  LEFT OUTER JOIN "main"."deal_stages" AS "dealStage" ON "v_deal_view"."stage_id" = "dealStage"."id" 
WHERE 
  "v_deal_view"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
LIMIT 1;
```
</details>


<details>
  <summary>GET /resources (include Delivery department resources and their locationKey)</summary>

### Relation

```ts
@belongsTo(
  () => Department,
  {name: 'department'},
  {
    name: 'department_id',
    required: true,
  },
)
departmentId: string;

@belongsTo(
  () => Location,
  {name: 'location'},
  {
    name: 'location_id',
    required: true,
  },
)
locationId: string;
```

### Filter 

```yaml
{
  "order": "name",
  "include": [
    {
      "relation": "department",
      "scope": {
        "where": {
          "name": "Delivery"
        },
        "fields": { "name": true }
      },
      "required": true
    },
    {
      "relation": "location",
      "scope": {
        "fields": { "locationKey": true }
      }
    }
  ]
}
```

### Query

```sql
SELECT 
  "resources"."id", 
  "resources"."name", 
  /* ... other `resources` table columns ... */
  
  "department"."id" AS "department.id", 
  "department"."name" AS "department.name", 
  "location"."id" AS "location.id", 
  "location"."location_key" AS "location.locationKey" 
FROM 
  "main"."resources" AS "resources" 
  INNER JOIN "main"."departments" AS "department" ON "resources"."department_id" = "department"."id" 
  AND "department"."name" = 'Delivery' 
  LEFT OUTER JOIN "main"."locations" AS "location" ON "resources"."location_id" = "location"."id" 
WHERE 
  "resources"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
ORDER BY 
  "resources"."name" ASC;


```
</details>


## @hasMany
## @hasMany through
## @hasOne
## @referencesMany
