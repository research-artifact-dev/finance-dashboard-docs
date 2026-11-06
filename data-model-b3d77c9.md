<!-- loiob3d77c9f57c846db8dd683585fa420a3 -->

# Data Model

The Finance Intelligence Dashboard uses seven Core Data Services \(CDS\) entities defined in `db/schema.cds`. Each entity maps directly to an OData V4 entity set exposed by the FinanceService, and all entities are seeded with mock data in CSV files under `db/data/` for local development.

The data model is intentionally flat to maximize OData query performance and simplify frontend binding. Relationships between entities are navigated at the service level through OData query parameters.



<a name="loiob3d77c9f57c846db8dd683585fa420a3__section_entities"/>

## Entity Definitions

The CDS schema defines the following entities in the `db` namespace:

### Invoices

The `Invoices` entity is the central business object. It captures the full lifecycle of a single invoice, from the raw vendor amount through tax calculation, AI validation, compliance scoring, and final disposition.

**Invoice Entity Fields**


<table>
<tr>
<th valign="top">

Field

</th>
<th valign="top">

Type

</th>
<th valign="top">

Description

</th>
</tr>
<tr>
<td valign="top">

`ID`

</td>
<td valign="top">

String \(key\)

</td>
<td valign="top">

Unique invoice identifier

</td>
</tr>
<tr>
<td valign="top">

`vendor`

</td>
<td valign="top">

String

</td>
<td valign="top">

Vendor or supplier name

</td>
</tr>
<tr>
<td valign="top">

`amount`

</td>
<td valign="top">

Decimal\(15, 2\)

</td>
<td valign="top">

Gross invoice amount

</td>
</tr>
<tr>
<td valign="top">

`tax`

</td>
<td valign="top">

String

</td>
<td valign="top">

Tax type identifier \(for example, VAT, GST\)

</td>
</tr>
<tr>
<td valign="top">

`taxAmount`

</td>
<td valign="top">

Decimal\(15, 2\)

</td>
<td valign="top">

Calculated tax amount

</td>
</tr>
<tr>
<td valign="top">

`score`

</td>
<td valign="top">

Integer

</td>
<td valign="top">

AI-assigned validation score \(0–100\)

</td>
</tr>
<tr>
<td valign="top">

`status`

</td>
<td valign="top">

String

</td>
<td valign="top">

Processing status \(for example, Pending, Approved, Rejected\)

</td>
</tr>
<tr>
<td valign="top">

`date`

</td>
<td valign="top">

Date

</td>
<td valign="top">

Invoice date

</td>
</tr>
<tr>
<td valign="top">

`complianceScore`

</td>
<td valign="top">

Integer

</td>
<td valign="top">

Compliance assessment score \(0–100\)

</td>
</tr>
<tr>
<td valign="top">

`riskLevel`

</td>
<td valign="top">

String

</td>
<td valign="top">

Risk classification \(High, Medium, Low\)

</td>
</tr>
<tr>
<td valign="top">

`aiVerdict`

</td>
<td valign="top">

String

</td>
<td valign="top">

AI agent decision summary

</td>
</tr>
<tr>
<td valign="top">

`details`

</td>
<td valign="top">

String

</td>
<td valign="top">

Human-readable invoice summary

</td>
</tr>
<tr>
<td valign="top">

`referenceNumber`

</td>
<td valign="top">

String

</td>
<td valign="top">

External reference or purchase order number

</td>
</tr>
<tr>
<td valign="top">

`currency`

</td>
<td valign="top">

String

</td>
<td valign="top">

ISO 4217 currency code

</td>
</tr>
<tr>
<td valign="top">

`validationErrors`

</td>
<td valign="top">

String

</td>
<td valign="top">

Comma-separated list of AI validation errors

</td>
</tr>
<tr>
<td valign="top">

`vatRate`

</td>
<td valign="top">

String

</td>
<td valign="top">

Applicable VAT rate as a percentage string

</td>
</tr>
<tr>
<td valign="top">

`netAmount`

</td>
<td valign="top">

Decimal\(15, 2\)

</td>
<td valign="top">

Net amount before tax

</td>
</tr>
</table>

### Supporting Entities

The following entities provide supporting reference data for the dashboard:

**Supporting Entities**


<table>
<tr>
<th valign="top">

Entity

</th>
<th valign="top">

Purpose

</th>
<th valign="top">

Key Fields

</th>
</tr>
<tr>
<td valign="top">

`ComplianceScores`

</td>
<td valign="top">

Category-wise compliance metrics shown as progress bars

</td>
<td valign="top">

`category`, `percentage`, `color`, `description`

</td>
</tr>
<tr>
<td valign="top">

`Alerts`

</td>
<td valign="top">

Risk and anomaly alerts with severity classification

</td>
<td valign="top">

`title`, `severity`, `status`, `details`, `color`

</td>
</tr>
<tr>
<td valign="top">

`AgentStatus`

</td>
<td valign="top">

Real-time operational status of each AI agent

</td>
<td valign="top">

`name`, `subtitle`, `status`, `icon`

</td>
</tr>
<tr>
<td valign="top">

`PipelineStages`

</td>
<td valign="top">

Definition and current status of each processing pipeline stage

</td>
<td valign="top">

`title`, `subtitle`, `status`, `order`

</td>
</tr>
<tr>
<td valign="top">

`KPIMetrics`

</td>
<td valign="top">

Key performance indicator values with trend direction

</td>
<td valign="top">

`title`, `value`, `trend`, `trendDir`

</td>
</tr>
<tr>
<td valign="top">

`MonthlyTrends`

</td>
<td valign="top">

Historical monthly invoice volume for bar chart visualization

</td>
<td valign="top">

`month`, `value`, `year`

</td>
</tr>
</table>



<a name="loiob3d77c9f57c846db8dd683585fa420a3__section_mock_data"/>

## Mock Data

All entities are seeded with CSV files in `db/data/`. The file naming convention is `db-EntityName.csv`. These files are loaded automatically when you run the application in development mode using `npm run watch`.

The mock data includes sample invoices with varied compliance scores and risk levels, so you can test all dashboard states without connecting to a real data source.

> ### Tip:  
> To add your own test invoices, edit `db/data/db-Invoices.csv` directly and restart the development server. The SAP CAP runtime reloads CSV data on restart.



<a name="loiob3d77c9f57c846db8dd683585fa420a3__section_cds_source"/>

## CDS Source

The complete CDS data model is in `db/schema.cds`. The full namespace is `db`, and all entities are referenced in the service layer as `db.EntityName`.

```cds
namespace db;

entity Invoices {
    key ID               : String;
        vendor           : String;
        amount           : Decimal(15, 2);
        tax              : String;
        taxAmount        : Decimal(15, 2);
        score            : Integer;
        status           : String;
        date             : Date;
        complianceScore  : Integer;
        riskLevel        : String;
        aiVerdict        : String;
        details          : String;
        referenceNumber  : String;
        currency         : String;
        validationErrors : String;
        vatRate          : String;
        netAmount        : Decimal(15, 2);
}
```

For guidance on OData support in SAP CAP, see [Maintaining OData Services in SAP HANA Cloud](https://help.sap.com/docs/HANA_CLOUD_DATABASE/b9902c314aef4afb8f7a29bf8c5b37b3/78606fc13a6b41e5b654ca5f289351ca.html "OData service maintenance guide for SAP HANA Cloud") :arrow_upper_right:.

**Related Information**  


[Overview](overview-9a752eb.md "Finance Intelligence Dashboard system overview and architecture")

[OData Service Layer](odata-service-2352883.md "FinanceService endpoints and service implementation")

[Maintaining OData Services in SAP HANA Cloud](https://help.sap.com/docs/HANA_CLOUD_DATABASE/b9902c314aef4afb8f7a29bf8c5b37b3/78606fc13a6b41e5b654ca5f289351ca.html "OData service maintenance for SAP HANA Cloud") :arrow_upper_right:
