<!-- loio53ad056b1a184ce6828c33ee2d86ee5a -->

# UI Components

The frontend of the Finance Intelligence Dashboard is built with SAP UI5 following the Model-View-Controller \(MVC\) pattern. The application is structured as a standard SAP UI5 Component with XML views, reusable XML fragments, a central controller, and a formatter utility. All UI elements conform to the SAP Horizon theme and SAP Fiori design guidelines.

The frontend lives in `app/finance-dashboard/webapp/` and is served directly by the SAP CAP development server at `http://localhost:4004`.



<a name="loio53ad056b1a184ce6828c33ee2d86ee5a__section_mvc_structure"/>

## MVC Structure

SAP UI5 uses the MVC \(Model-View-Controller\) pattern to separate data, presentation, and business logic. In this application, the pattern maps to three distinct layers:

-   **Models** — OData V4 models configured in `manifest.json` bind directly to the FinanceService
-   **Views** — XML files in `view/` define the UI structure declaratively
-   **Controllers** — JavaScript files in `controller/` handle user interactions and data manipulation

This separation makes it easy to update the UI layout without touching business logic, and to modify controller logic without redesigning views.

For SAP Fiori presentation tier guidance, see [Presentation Tier Guidance](https://help.sap.com/docs/EXTENSION_ARCHITECTURE_GUIDE/0d65087f886b45488953c28f0ad29c05/2e520f8992014a9c8038f79d8858a513.html "SAP Fiori presentation tier selection guide") :arrow_upper_right:.



<a name="loio53ad056b1a184ce6828c33ee2d86ee5a__section_views"/>

## XML Views

The application uses two XML views:

**XML Views**


<table>
<tr>
<th valign="top">

File

</th>
<th valign="top">

Purpose

</th>
</tr>
<tr>
<td valign="top">

`App.view.xml`

</td>
<td valign="top">

Root view — initializes the router and provides the application shell

</td>
</tr>
<tr>
<td valign="top">

`Dashboard.view.xml`

</td>
<td valign="top">

Main dashboard view — lays out all panels, KPI cards, pipeline, and chart

</td>
</tr>
</table>

`Dashboard.view.xml` is the primary view. It aggregates all XML fragments into a single dashboard layout, binding each section to a specific OData entity set via named models.



<a name="loio53ad056b1a184ce6828c33ee2d86ee5a__section_fragments"/>

## XML Fragments

XML fragments allow you to decompose complex views into reusable, self-contained UI blocks. Each fragment in this application maps to one dashboard panel:

**XML Fragments**


<table>
<tr>
<th valign="top">

Fragment File

</th>
<th valign="top">

Dashboard Panel

</th>
<th valign="top">

Data Binding

</th>
</tr>
<tr>
<td valign="top">

`PipelineStage.fragment.xml`

</td>
<td valign="top">

7-stage processing pipeline with status indicators

</td>
<td valign="top">

`/PipelineStages`

</td>
</tr>
<tr>
<td valign="top">

`KPICards.fragment.xml`

</td>
<td valign="top">

KPI summary cards \(invoices, tax, compliance, anomalies\)

</td>
<td valign="top">

`/KPIMetrics`

</td>
</tr>
<tr>
<td valign="top">

`ComplianceScore.fragment.xml`

</td>
<td valign="top">

Category compliance breakdown with progress bars

</td>
<td valign="top">

`/ComplianceScores`

</td>
</tr>
<tr>
<td valign="top">

`AlertsList.fragment.xml`

</td>
<td valign="top">

Severity-based risk and anomaly alerts list

</td>
<td valign="top">

`/Alerts`

</td>
</tr>
<tr>
<td valign="top">

`AgentStatus.fragment.xml`

</td>
<td valign="top">

Real-time AI agent operational status panel

</td>
<td valign="top">

`/AgentStatus`

</td>
</tr>
<tr>
<td valign="top">

`MonthlyChart.fragment.xml`

</td>
<td valign="top">

Bar chart of monthly invoice volume

</td>
<td valign="top">

`/MonthlyTrends`

</td>
</tr>
<tr>
<td valign="top">

`InvoiceDetailDialog.fragment.xml`

</td>
<td valign="top">

Detail dialog with AI verdict, risk level, and approve/reject actions

</td>
<td valign="top">

Selected `Invoices` record

</td>
</tr>
</table>



<a name="loio53ad056b1a184ce6828c33ee2d86ee5a__section_controller"/>

## Dashboard Controller

`Dashboard.controller.js` is the single controller for the main dashboard view. It handles:

-   Loading all OData entity sets on initialization
-   Responding to invoice row selection and opening the `InvoiceDetailDialog` fragment
-   Processing approve and reject actions from the detail dialog
-   Refreshing KPI and alert data after user actions



<a name="loio53ad056b1a184ce6828c33ee2d86ee5a__section_formatter"/>

## Formatter Utility

`utils/formatter.js` provides pure data transformation functions that are called from XML view bindings. Formatters are stateless and testable in isolation. Common formatter functions in this application include:

-   Converting raw `riskLevel` strings to semantic color identifiers \(for example, `"High"` → `"Error"`\)
-   Formatting decimal amounts to localized currency strings
-   Translating `status` codes to human-readable labels and icon sources
-   Computing progress bar values from integer compliance percentages



<a name="loio53ad056b1a184ce6828c33ee2d86ee5a__section_design_system"/>

## Design System

The application uses the SAP Horizon theme with the following design tokens:

**Design Tokens**


<table>
<tr>
<th valign="top">

Token

</th>
<th valign="top">

Value

</th>
<th valign="top">

Usage

</th>
</tr>
<tr>
<td valign="top">

Primary blue

</td>
<td valign="top">

`#0070F2`

</td>
<td valign="top">

Primary actions, highlights, and active states

</td>
</tr>
<tr>
<td valign="top">

Success green

</td>
<td valign="top">

`#00C853`

</td>
<td valign="top">

Approved invoices and passing compliance scores

</td>
</tr>
<tr>
<td valign="top">

Warning orange

</td>
<td valign="top">

`#FF9800`

</td>
<td valign="top">

Medium-severity alerts and caution states

</td>
</tr>
<tr>
<td valign="top">

Error red

</td>
<td valign="top">

`#D32F2F`

</td>
<td valign="top">

High-severity alerts, rejected invoices, and validation errors

</td>
</tr>
<tr>
<td valign="top">

Background gray

</td>
<td valign="top">

`#f5f6f7`

</td>
<td valign="top">

Card backgrounds and panel fills

</td>
</tr>
</table>

Typography uses the SAP 72 font family at 13–18px, with 600 font weight for titles and uppercase labels at 11–12px.

**Related Information**  


[Overview](overview-9a752eb.md "Finance Intelligence Dashboard system overview and architecture")

[AI Agents and Processing Pipeline](ai-agents-pipeline-641aad7.md "AI agent roles and 7-stage pipeline")

[Presentation Tier Guidance](https://help.sap.com/docs/EXTENSION_ARCHITECTURE_GUIDE/0d65087f886b45488953c28f0ad29c05/2e520f8992014a9c8038f79d8858a513.html "SAP Fiori frontend tier selection and guidance") :arrow_upper_right:

[Providing Access to the SAP Fiori Application](https://help.sap.com/docs/BTP/65de2977205c403bbc107264b8eccf4b/b569abb158934306a65f3eb38f86ffba.html "Fiori Launchpad access configuration on SAP BTP") :arrow_upper_right:
