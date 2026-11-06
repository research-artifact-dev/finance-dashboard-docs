<!-- loio2352883876c04552b4ee8a8d260dbd67 -->

# OData Service Layer

The Finance Intelligence Dashboard exposes all business data through a single OData V4 service called `FinanceService`. The service is defined in `srv/service.cds` using CDS projections and implemented in `srv/service.js` with custom event handlers for logging, error handling, and post-processing.

In development mode, the service is available at `http://localhost:4004/finance/`. In production on SAP BTP, the path is resolved through the Cloud Foundry route configured in `mta.yaml`.



<a name="loio2352883876c04552b4ee8a8d260dbd67__section_service_definition"/>

## Service Definition

The `FinanceService` is defined as a set of CDS projections on the underlying database entities. This approach means you can evolve the data model and service independently — projection-level annotations or filters can be applied without changing the core schema.

```cds
using db from '../db/schema';

service FinanceService {
    entity Invoices         as projection on db.Invoices;
    entity ComplianceScores as projection on db.ComplianceScores;
    entity Alerts           as projection on db.Alerts;
    entity AgentStatus      as projection on db.AgentStatus;
    entity PipelineStages   as projection on db.PipelineStages;
    entity KPIMetrics       as projection on db.KPIMetrics;
    entity MonthlyTrends    as projection on db.MonthlyTrends;
}
```

For a full reference on OData V4 services in SAP CAP, see [Maintaining OData Services in SAP HANA Cloud](https://help.sap.com/docs/HANA_CLOUD_DATABASE/b9902c314aef4afb8f7a29bf8c5b37b3/78606fc13a6b41e5b654ca5f289351ca.html "OData service maintenance for SAP HANA Cloud applications") :arrow_upper_right:.



<a name="loio2352883876c04552b4ee8a8d260dbd67__section_endpoints"/>

## Available Endpoints

When the application is running, the following OData entity set endpoints are available under the `/finance/` path:

**OData V4 Entity Set Endpoints**


<table>
<tr>
<th valign="top">

Endpoint

</th>
<th valign="top">

Entity Set

</th>
<th valign="top">

Description

</th>
</tr>
<tr>
<td valign="top">

`/finance/Invoices`

</td>
<td valign="top">

Invoices

</td>
<td valign="top">

Invoice records with AI scores, risk levels, and compliance data

</td>
</tr>
<tr>
<td valign="top">

`/finance/ComplianceScores`

</td>
<td valign="top">

ComplianceScores

</td>
<td valign="top">

Category-wise compliance metric percentages

</td>
</tr>
<tr>
<td valign="top">

`/finance/Alerts`

</td>
<td valign="top">

Alerts

</td>
<td valign="top">

Risk and anomaly alerts with severity and status

</td>
</tr>
<tr>
<td valign="top">

`/finance/AgentStatus`

</td>
<td valign="top">

AgentStatus

</td>
<td valign="top">

Operational status of each AI processing agent

</td>
</tr>
<tr>
<td valign="top">

`/finance/PipelineStages`

</td>
<td valign="top">

PipelineStages

</td>
<td valign="top">

Processing pipeline stage definitions and current status

</td>
</tr>
<tr>
<td valign="top">

`/finance/KPIMetrics`

</td>
<td valign="top">

KPIMetrics

</td>
<td valign="top">

Key performance indicator values with trend direction

</td>
</tr>
<tr>
<td valign="top">

`/finance/MonthlyTrends`

</td>
<td valign="top">

MonthlyTrends

</td>
<td valign="top">

Historical monthly invoice volume data for charting

</td>
</tr>
</table>

To browse the service metadata, navigate to `http://localhost:4004/finance/$metadata` during development.



<a name="loio2352883876c04552b4ee8a8d260dbd67__section_service_implementation"/>

## Service Implementation

The `srv/service.js` file adds custom event handlers on top of the default CAP auto-CRUD behavior. The implementation covers three concerns: pre-read logging, custom KPI read handling, and global error and post-read telemetry.

### KPIMetrics Custom Handler

The `KPIMetrics` entity uses a custom `READ` handler instead of relying solely on the CAP generic handler. This pattern is used when you need to intercept or transform data before it reaches the client.

```js
srv.on('READ', 'KPIMetrics', async (req) => {
    const result = await req.query;
    return result;
});
```

### Global Error Handler

The service registers a global error handler that logs the entity name and stack trace for any runtime error:

```js
srv.on('error', (err, req) => {
    console.error('GLOBAL ERROR:', err.message);
    console.error('   Entity:', req.target?.name);
    console.error('   Stack:', err.stack);
});
```

### Post-Read Logging

An `after('READ', '*')` hook runs after every successful read across all entities. It logs the entity name and the record count returned, which is useful for performance diagnostics:

```js
srv.after('READ', '*', (data, req) => {
    const entityName = req.target.name;
    const count = Array.isArray(data) ? data.length : 1;
    console.log(`Returned ${count} records for ${entityName}`);
    return data;
});
```

!!! note
    The global `after('READ', '*')` hook introduces a small overhead on every read operation. In high-throughput production environments, consider replacing it with entity-specific hooks on only the entities that require monitoring.



<a name="loio2352883876c04552b4ee8a8d260dbd67__section_odata_queries"/>

## OData Query Examples

You can use standard OData V4 query parameters to filter and sort results during development. The following examples demonstrate typical queries:

**Filter high-risk invoices:**

```
/finance/Invoices?$filter=riskLevel eq 'High'
```

**Sort invoices by compliance score descending:**

```
/finance/Invoices?$orderby=complianceScore desc
```

**Select specific fields from KPI metrics:**

```
/finance/KPIMetrics?$select=title,value,trendDir
```

**Related Information**  


[Data Model](data-model-b3d77c9.md "CDS entity definitions and field descriptions")

[Overview](overview-9a752eb.md "Finance Intelligence Dashboard system overview and architecture")

[Working with the SAP Cloud Application Programming Model](https://help.sap.com/docs/HANA_CLOUD_DATABASE/b9902c314aef4afb8f7a29bf8c5b37b3/166f4fbbcbc64df3bd74220fd75c5b1e.html "SAP CAP overview on SAP Help Portal") :arrow_upper_right:

[Maintaining OData Services in SAP HANA Cloud](https://help.sap.com/docs/HANA_CLOUD_DATABASE/b9902c314aef4afb8f7a29bf8c5b37b3/78606fc13a6b41e5b654ca5f289351ca.html "OData service maintenance guide") :arrow_upper_right:
