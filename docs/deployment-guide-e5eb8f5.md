<!-- loioe5eb8f58c65146fb977571db9c4079a0 -->

# Deployment Guide

The Finance Intelligence Dashboard runs locally for development using SQLite and the SAP CAP watch mode server. For production, it deploys to SAP Business Technology Platform \(BTP\) via Cloud Foundry using a Multi-Target Application \(MTA\) archive. An alternative deployment path to SAP HANA is also available for organizations already using SAP HANA Platform.

This guide covers both the local development setup and the full BTP deployment procedure.



<a name="loioe5eb8f58c65146fb977571db9c4079a0__section_prerequisites"/>

## Prerequisites

Before you start, ensure the following are available on your machine:

-   Node.js version 18 or higher
-   npm version 8 or higher
-   SAP CDS CLI, installed globally: `npm install -g @sap/cds-dk`
-   For BTP deployment: Cloud Foundry CLI with the MultiApps plugin installed
-   For BTP deployment: An active SAP BTP subaccount with a Cloud Foundry space



<a name="loioe5eb8f58c65146fb977571db9c4079a0__section_local_dev"/>

## Local Development Setup

To run the application locally:

1.  Clone or copy the project to your local machine.

2.  Install all Node.js dependencies:

    ```sh
    npm install
    ```

3.  Start the development server with file watching enabled:

    ```sh
    npm run watch
    ```

4.  Open your browser and navigate to `http://localhost:4004`.

5.  Choose the `finance-dashboard` tile to open the application.

The `npm run watch` command starts the SAP CAP server in development mode. It loads the SQLite in-memory database, seeds it with CSV mock data from `db/data/`, and serves both the OData service and the SAP UI5 frontend from the same port.

!!! note
    In development mode, all data is held in memory and resets on each server restart. To persist changes across restarts, configure a file-based SQLite database or switch to SAP HANA.

**Available Development Commands**


<table>
<tr>
<th valign="top">

Command

</th>
<th valign="top">

Description

</th>
</tr>
<tr>
<td valign="top">

`npm start`

</td>
<td valign="top">

Run in production mode \(no file watching\)

</td>
</tr>
<tr>
<td valign="top">

`npm run watch`

</td>
<td valign="top">

Run in development mode with automatic reload on file changes

</td>
</tr>
<tr>
<td valign="top">

`npm run build`

</td>
<td valign="top">

Build the project for production packaging

</td>
</tr>
<tr>
<td valign="top">

`npm run serve`

</td>
<td valign="top">

Serve with in-memory SQLite database

</td>
</tr>
<tr>
<td valign="top">

`npm run deploy`

</td>
<td valign="top">

Deploy the database schema to SAP HANA

</td>
</tr>
</table>



<a name="loioe5eb8f58c65146fb977571db9c4079a0__section_btp_deployment"/>

## SAP BTP Cloud Foundry Deployment

The application is packaged as a Multi-Target Application using `mta.yaml`. The MTA format bundles the SAP CAP service module and the SAP UI5 frontend module into a single deployable archive.

For reference, see [Deploy the Migrated SAP CAP Application to SAP BTP](https://help.sap.com/docs/SAP_HANA_PLATFORM/58d81eb4c9bc4899ba972c9fe7a1a115/3a6ba417f14a4472bd2044e2ddf60c45.html "Guide for deploying SAP CAP applications to SAP HANA Cloud on SAP BTP") :arrow_upper_right:.

To deploy to SAP BTP Cloud Foundry:

1.  Log in to your Cloud Foundry organization and target your deployment space:

    ```sh
    cf login -a <api-endpoint>
    cf target -o <org> -s <space>
    ```

2.  Build the MTA archive using the MTA Build Tool:

    ```sh
    mbt build
    ```

    This creates a `.mtar` file under `mta_archives/`.

3.  Deploy the archive to Cloud Foundry:

    ```sh
    cf deploy mta_archives/finance-dashboard_1.0.0.mtar
    ```

4.  Monitor deployment progress in the terminal. After a successful deployment, the application URL is printed in the output.

For guidance on the `mta.yaml` descriptor format, see [Maintaining the Multitarget Application Development and Deployment Descriptors](https://help.sap.com/docs/HANA_CLOUD_DATABASE_CN/ebd4a88a3ca646748222f5308d31d816/b2e355a5137c4799932f776716b292c9.html "MTA descriptor reference for SAP HANA Cloud") :arrow_upper_right:.



<a name="loioe5eb8f58c65146fb977571db9c4079a0__section_hana_deployment"/>

## SAP HANA Deployment

If your organization uses SAP HANA Platform directly \(without Cloud Foundry MTA\), you can deploy the CAP database schema with a single CDS command:

```sh
cds deploy --to hana
```

This command generates the HDI \(HANA Deployment Infrastructure\) artifacts and pushes the schema to the connected SAP HANA instance. Ensure that the HANA connection details are configured in your `~/.cdsrc.json` or as environment variables before running this command.

!!! danger "Caution"
    Running `cds deploy --to hana` on an existing production database will apply schema migrations. Review the generated HDI artifacts before deploying to a production environment to avoid unintended data loss.



<a name="loioe5eb8f58c65146fb977571db9c4079a0__section_testing"/>

## Verifying the Deployment

After starting the server — locally or on BTP — you can verify that the OData service is running by navigating to the service document:

```
http://<host>:4004/finance/
```

To test individual entity sets, navigate to:

```
http://<host>:4004/finance/Invoices
```

A JSON response containing invoice records confirms that the service and database connection are working correctly.

**Related Information**  


[Overview](overview-9a752eb.md "Finance Intelligence Dashboard system overview and architecture")

[OData Service Layer](odata-service-2352883.md "FinanceService endpoints and testing")

[Deploy the Migrated SAP CAP Application to SAP BTP](https://help.sap.com/docs/SAP_HANA_PLATFORM/58d81eb4c9bc4899ba972c9fe7a1a115/3a6ba417f14a4472bd2044e2ddf60c45.html "SAP CAP BTP deployment guide") :arrow_upper_right:

[Maintaining the Multitarget Application Development and Deployment Descriptors](https://help.sap.com/docs/HANA_CLOUD_DATABASE_CN/ebd4a88a3ca646748222f5308d31d816/b2e355a5137c4799932f776716b292c9.html "MTA descriptor reference") :arrow_upper_right:
