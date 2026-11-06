<!-- loio641aad7456f448a091aefc31608692a6 -->

# AI Agents and Processing Pipeline

The Finance Intelligence Dashboard automates invoice processing through a multi-agent AI pipeline. Five specialized agents operate in sequence across seven pipeline stages, each performing a distinct function: validation, tax identification, tax calculation, risk assessment, and report generation.

Agent operational status is tracked in real time in the `AgentStatus` entity and displayed in the dashboard's AI Agent Status panel. If an agent encounters an error, the pipeline stage status updates immediately, giving finance operators instant visibility without polling.



<a name="loio641aad7456f448a091aefc31608692a6__section_agents"/>

## AI Agents

The following agents are registered in the `AgentStatus` entity and appear in the Agent Status panel:

**AI Agent Roles**


<table>
<tr>
<th valign="top">

Agent

</th>
<th valign="top">

Role

</th>
<th valign="top">

Output

</th>
</tr>
<tr>
<td valign="top">

Invoice Validator

</td>
<td valign="top">

Checks invoice structure, required fields, and vendor data integrity

</td>
<td valign="top">

`validationErrors` field on the Invoices entity; AI validation score

</td>
</tr>
<tr>
<td valign="top">

Tax Selector

</td>
<td valign="top">

Identifies the correct tax type \(for example, VAT, GST\) based on vendor jurisdiction

</td>
<td valign="top">

`tax` and `vatRate` fields on the Invoices entity

</td>
</tr>
<tr>
<td valign="top">

Tax Calculator

</td>
<td valign="top">

Computes tax amount and net amount from gross amount and identified tax rate

</td>
<td valign="top">

`taxAmount` and `netAmount` fields on the Invoices entity

</td>
</tr>
<tr>
<td valign="top">

Risk and Anomaly Scorer

</td>
<td valign="top">

Assigns a risk level and overall compliance score; flags anomalies for review

</td>
<td valign="top">

`riskLevel`, `score`, `complianceScore`, and Alerts entity records

</td>
</tr>
<tr>
<td valign="top">

Report Generator

</td>
<td valign="top">

Produces the AI verdict summary and populates KPI and trend data

</td>
<td valign="top">

`aiVerdict` field; KPIMetrics and MonthlyTrends entity updates

</td>
</tr>
</table>



<a name="loio641aad7456f448a091aefc31608692a6__section_pipeline"/>

## Seven-Stage Processing Pipeline

Each invoice moves through seven sequential stages. The `PipelineStages` entity stores the definition and current status of each stage. The dashboard renders these stages with visual flow connectors and status icons.

**Invoice Processing Pipeline Stages**


<table>
<tr>
<th valign="top">

Stage

</th>
<th valign="top">

Name

</th>
<th valign="top">

Agent Responsible

</th>
<th valign="top">

Output

</th>
</tr>
<tr>
<td valign="top">

1

</td>
<td valign="top">

Invoice Ingestion

</td>
<td valign="top">

\-

</td>
<td valign="top">

Invoice record created in the `Invoices` entity with status `Pending`

</td>
</tr>
<tr>
<td valign="top">

2

</td>
<td valign="top">

Structure Validation

</td>
<td valign="top">

Invoice Validator

</td>
<td valign="top">

Validation errors written; score assigned

</td>
</tr>
<tr>
<td valign="top">

3

</td>
<td valign="top">

Tax Identification

</td>
<td valign="top">

Tax Selector

</td>
<td valign="top">

Tax type and VAT rate populated

</td>
</tr>
<tr>
<td valign="top">

4

</td>
<td valign="top">

Tax Calculation

</td>
<td valign="top">

Tax Calculator

</td>
<td valign="top">

Tax amount and net amount computed

</td>
</tr>
<tr>
<td valign="top">

5

</td>
<td valign="top">

Risk and Anomaly Scoring

</td>
<td valign="top">

Risk and Anomaly Scorer

</td>
<td valign="top">

Risk level, compliance score, and alert records written

</td>
</tr>
<tr>
<td valign="top">

6

</td>
<td valign="top">

Report Generation

</td>
<td valign="top">

Report Generator

</td>
<td valign="top">

AI verdict written; KPI metrics and monthly trend updated

</td>
</tr>
<tr>
<td valign="top">

7

</td>
<td valign="top">

Review and Disposition

</td>
<td valign="top">

Finance user

</td>
<td valign="top">

Invoice approved or rejected via the detail dialog; status updated

</td>
</tr>
</table>



<a name="loio641aad7456f448a091aefc31608692a6__section_agent_status"/>

## Agent Status Monitoring

The `AgentStatus` entity tracks the runtime state of each agent. Each record has a `status` field that the dashboard reads and displays in the AI Agent Status panel. The status values used in the mock data are:

-   `Active` — the agent is running and processing invoices
-   `Idle` — the agent is available but has no current work
-   `Error` — the agent encountered a fault; check `details` for the error message

The dashboard refreshes agent status automatically. If you integrate a real AI agent backend, update the `AgentStatus` records via OData PATCH or through a custom CAP action to reflect actual runtime state.

> ### Tip:  
> In a production deployment, consider exposing a CAP action — for example, `triggerPipelineRun` — that triggers the agent pipeline for a batch of invoices and writes status updates asynchronously. This pattern keeps the UI decoupled from agent execution latency.

**Related Information**  


[Data Model](data-model-b3d77c9.md "CDS entity definitions including AgentStatus and PipelineStages")

[OData Service Layer](odata-service-2352883.md "FinanceService endpoints for AgentStatus and PipelineStages")

[UI Components](ui-components-53ad056.md "AgentStatus and PipelineStage UI fragments")

[Working with the SAP Cloud Application Programming Model](https://help.sap.com/docs/HANA_CLOUD_DATABASE/b9902c314aef4afb8f7a29bf8c5b37b3/166f4fbbcbc64df3bd74220fd75c5b1e.html "SAP CAP framework for building AI-integrated services") :arrow_upper_right:
