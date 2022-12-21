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


## @hasMany
## @hasMany through
## @hasOne
## @referencesMany
