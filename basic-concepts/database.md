---
description: Schema of Adbox database
---

# Database

All resource types in aidbox are stored in different tables, named with lowercased resource type name. All this tables have similar schema:

```text
CREATE TABLE "patient" (
  id text PRIMARY KEY,               // id of resource
  txid bigint not null,              // version id and logical transaction id
  ts timestamptz DEFAULT NOW(),      // last updated time
  resource_type text,                // resource type
  status resource_status not null,   // resource status
  resource jsonb not null            // resource body
);
```

You use DB Console to explore database:

```sql
select * from "entity" limit 10
```

As you see resources are stored as JSONB documents in **resource** column.

{% hint style="info" %}
Resources are stored in [Aidbox JSON format](aidbox-and-fhir-formats.md), which is more friendly for storage and converted on-fly into FHIR in REST API!
{% endhint %}

 You can access attributes of resources using [PostgreSQL JSON functions](https://www.postgresql.org/docs/11/functions-json.html):

```sql
SELECT
   resource#>>'{name,0,famly}' as last_name,
   resource#>>'{name,0,given,0}' as first_name
FROM "patient"
LIMIT 10
   
```

### Custom Queries

You can define and expose over REST API sophisticated queries in SQL on FHIR data using [Custom Queries](search-1/custom-search.md). 

### Tutorials

Checkout our video tutorial about SQL on FHIR in PostgreSQL:

{% embed url="https://www.youtube.com/watch?v=zgU5c3RwjD4" %}



