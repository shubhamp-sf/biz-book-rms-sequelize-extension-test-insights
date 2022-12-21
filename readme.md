# Relations

## @belongsTo

<details>
  <summary>GET /deals  <b><i> (include related project) </i></b> </summary>

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
  <summary>GET /deals <b><i>(include related project and dealStage)</i></b></summary>

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
  <summary>GET /deal-views <b><i>(include related dealStage)</i></b></summary>

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
  <summary>GET /resources <b><i>(include Delivery department resources and their locationKey)</i></b></summary>

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

```json
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

<details>
  <summary>GET /clients <b><i>(include projects where project status != 4)</i></b></summary>

### Relation

```ts
@hasMany(() => Project, {keyTo: 'clientId'})
projects: Project[];
```

### Filter 

```json
{
  "limit": 5,
  "order": "id",
  "include": [
    {
      "relation": "projects",
      "scope": {
        "where": { "status": { "neq": 4 } }
      }
    }
  ]
}
```

### Query

```sql
SELECT 
  "clients".*, 
  "projects"."id" AS "projects.id", 
  "projects"."name" AS "projects.name", 
  /* ... other `projects` table columns ... */
FROM 
  (
    SELECT 
      "clients"."id", 
      "clients"."name", 
      /* ... other `projects` table columns ... */
    FROM 
      "main"."clients" AS "clients" 
    WHERE 
      "clients"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
    ORDER BY 
      "clients"."id" ASC 
    LIMIT 
      5
  ) AS "clients" 
  LEFT OUTER JOIN "main"."projects" AS "projects" ON "clients"."id" = "projects"."client_id" 
  AND "projects"."status" != 4 
ORDER BY 
  "clients"."id" ASC;

```
</details>

<details>
  <summary>GET /deals <b><i>(include dealContacts)</i></b></summary>

### Relation

```ts
@hasMany(() => DealContact, {keyTo: 'dealId'})
dealContacts: DealContact[];
```

### Filter 

```json
{
  "limit": 1,
  "order": "id",
  "include": [
    {
      "relation": "dealContacts"
    }
  ]
}
```

### Query

```sql
SELECT 
  "deals".*, 
  "dealContacts"."id" AS "dealContacts.id", 
  "dealContacts"."contact_id" AS "dealContacts.contactId", 
  /* ... other `deal_contacts` table columns ... */
  
FROM 
  (
    SELECT 
      "deals"."id", 
      "deals"."name", 
      /* ... all `deals` table columns ... */
    FROM 
      "main"."deals" AS "deals" 
    WHERE 
      "deals"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
    ORDER BY 
      "deals"."id" ASC 
    LIMIT 
      1
  ) AS "deals" 
  LEFT OUTER JOIN "main"."deal_contacts" AS "dealContacts" ON "deals"."id" = "dealContacts"."deal_id" 
ORDER BY 
  "deals"."id" ASC;
```
</details>

## @hasMany through

<details>
  <summary>GET /contacts <b><i>(include deals through dealContacts)</i></b></summary>

### Relation

```ts
@hasMany(() => Deal, {
  through: {model: () => DealContact, keyFrom: 'contactId', keyTo: 'dealId'},
})
deals: Deal[];
```

### Filter 

```json
{
  "limit": 4,
  "order": "id",
  "include": [
    {
      "relation": "deals",
      "scope": {
        "fields": { "owner": true, "type": true }
      }
    }
  ]
}
```

### Query
```sql
SELECT 
  "contacts".*, 
  "deals"."id" AS "deals.id", 
  "deals"."owner" AS "deals.owner", 
  "deals"."type" AS "deals.type", 
  
  "deals->deal_contacts"."contact_id" AS "deals.deal_contacts.contactId", 
  "deals->deal_contacts"."is_primary" AS "deals.deal_contacts.isPrimary", 
  /* ... other `deal_contacts` table columns ... */
  
FROM 
  (
    SELECT 
      "contacts"."id", 
      /* ... all `contacts` table columns ... */
    FROM 
      "main"."contacts" AS "contacts" 
    WHERE 
      "contacts"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
    LIMIT 
      4
  ) AS "contacts" 
  LEFT OUTER JOIN (
    "main"."deal_contacts" AS "deals->deal_contacts" 
    INNER JOIN "main"."deals" AS "deals" ON "deals"."id" = "deals->deal_contacts"."deal_id"
  ) ON "contacts"."id" = "deals->deal_contacts"."contact_id";
```
</details>

> Note: The response will contain the through model data, which is something that differs from how loopback returned the data with same filter

## @hasOne
> Found no model using @hasOne

## @referencesMany

<details>
  <summary>GET /project-allocations <b><i>(include resourceAllocations referenced by resourceAllocationIds)</i></b></summary>

### Relation

```ts
 @referencesMany(
  () => ResourceAllocationView,
  {name: 'resourceAllocations'},
  {name: 'resource_allocation_ids'},
)
resourceAllocationIds: string[];
```

### Filter 

```json
{
  "order": "id",
  "include": [
    {
      "relation": "resourceAllocations"
    }
  ]
}
```

### Query

1.
```sql
SELECT 
  "id", 
  "resource_id" AS "resourceId", 
  /* ... other `v_project_allocations` columns ... */
FROM 
  "main"."v_project_allocations" AS "v_project_allocations" 
WHERE 
  "v_project_allocations"."tenant_id" = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee' 
ORDER BY 
  "v_project_allocations"."id" ASC;

```

2. 
```sql
SELECT
  "id", 
  "resource_id"
  /* ... all `v_resource_allocations` columns ... */
FROM 
  "main"."v_resource_allocations" 
WHERE 
  "id" IN (
    $1, $2, $3, $4, $5
  ) 
ORDER BY 
  "id"

```
</details>
