# CarePlus Ticket Insights
### Automated AWS Data Pipeline | Amazon Redshift | Power BI

CarePlus Ticket Insights is an end-to-end cloud analytics project that transforms customer-support ticket data and application logs into a centralized, analytics-ready data warehouse and an interactive Power BI reporting experience.

The project demonstrates practical data engineering and business intelligence capabilities, including multi-source data ingestion, automated ETL orchestration, incremental data loading into Amazon Redshift, analytical data modeling, and dashboard development.

## Project Overview

Support and operations teams need a consistent view of ticket demand, service performance, agent workload, and application health. When ticket records, log files, and reporting data are handled separately, it becomes harder to build a unified view and maintain repeatable reporting workflows.

This project addresses that challenge by building an AWS-based data pipeline that collects, transforms, and organizes source data for analysis, then presents key operational metrics through Power BI.

## Key Contributions

- **Automated data transformation:** Implemented an event-driven transformation workflow using Amazon S3, AWS Lambda, and AWS Glue. Uploading source data to S3 can trigger the relevant processing workflow, reducing the need to start transformation jobs manually.
- **Incremental data loading into Amazon Redshift:** Implemented an incremental loading approach for the data warehouse so that new data can be processed as part of the ongoing pipeline, rather than relying only on full reloads.
- **Built a cloud-based ETL pipeline:** Organized source data into raw and processed S3 zones and used AWS Glue to validate, clean, standardize, and transform datasets.
- **Prepared analytics-ready data:** Stored curated datasets in Parquet format and loaded data into Amazon Redshift Serverless for warehouse analytics.
- **Developed Power BI reporting:** Built a semantic model and interactive reports for support-ticket performance and application-log monitoring.
- **Enabled SQL-based analysis:** Used Amazon Athena and Redshift SQL to support ad hoc queries and analytical workflows.

## Business Challenge

Support leaders need reliable visibility into ticket volume, status, service performance, agent workload, and the technical signals behind customer interactions. In this project scenario, relevant information was distributed across a support database, daily log files, spreadsheets, and ad hoc SQL analysis.

The goal was to consolidate these sources into a repeatable analytics workflow and make the resulting information easier to explore through a shared reporting layer.

## Solution at a Glance

The solution brings together two analytical perspectives:

- **Support performance:** Ticket volume, status, channel mix, agent workload, resolution time, and interaction count.
- **Operational health:** Log volume, error signals, CPU utilization, response time, components, and user-agent activity.

The resulting model supports questions such as:

- Which channels generate the most support demand?
- How many tickets are open, resolved, or escalated?
- Where are resolution times or agent workloads highest?
- What application-performance or error patterns appear in the operational logs?

## Architecture

<p align="center">
  <img src="aws_project_pipeline.svg" alt="CarePlus Ticket Insights AWS data pipeline architecture" width="100%">
</p>

### Data Flow

1. **Ingest source data** — Extract support-ticket records from the operational database and collect daily support-log files.
2. **Land raw data** — Store incoming data in Amazon S3 raw zones.
3. **Automate transformation** — Use S3-triggered AWS Lambda orchestration to start AWS Glue jobs that validate, clean, standardize, and transform source datasets.
4. **Publish curated data** — Write analytics-ready data in Parquet format to S3 processed zones.
5. **Load the warehouse incrementally** — Load curated data into Amazon Redshift Serverless using the project's incremental data-loading workflow and SQL/`COPY`-based loading patterns.
6. **Query and analyze** — Use Amazon Redshift for centralized warehouse analysis and Amazon Athena for ad hoc SQL queries.
7. **Visualize insights** — Connect Power BI to the warehouse and publish a semantic model with interactive reports.

AWS IAM roles provide service access across the pipeline.

## Live Power BI Dashboard

Explore the published interactive report: [**Open the CarePlus Ticket Insights Live Dashboard**](https://app.powerbi.com/view?r=eyJrIjoiOTFkODE1MDAtZTEwMS00OTliLTgzZGEtNmZhNGQ5NzY2YWUxIiwidCI6ImM2ZTU0OWIzLTVmNDUtNDAzMi1hYWU5LWQ0MjQ0ZGM1YjJjNCJ9).

## Dashboard Preview

### Support Tickets

<p align="center">
  <img src="screenshots/Screenshot%202026-10-29%20205104.png" alt="CarePlus support tickets dashboard" width="100%">
</p>

### Support Logs

<p align="center">
  <img src="screenshots/Screenshot%202026-10-29%20205118.png" alt="CarePlus support logs dashboard" width="100%">
</p>

## Data Model and Analytical Measures

The Power BI semantic model links two fact tables through shared ticket and date dimensions.

| Table | Purpose | Example fields |
|---|---|---|
| `support_tickets` | Customer-support case activity | Ticket ID, creation and resolution timestamps, agent, priority, channel, status, category, interactions |
| `support_logs` | Application and operational events | Event timestamp, component, log level, ticket/session IDs, response time, CPU, error flag, user agent |
| `dim_tickets` | Shared ticket key | Ticket ID across ticket and log data |
| `dim_date` | Consistent date filtering and trends | Date, year, month, day, weekday |

Key DAX measures include total, resolved, open, and escalated tickets; average resolution time; average interactions; active agents; total logs; distinct logged tickets; average CPU usage; and average response time.

## Technology Stack

| Area | Technologies and practices |
|---|---|
| Data ingestion | Python notebooks, database extraction, file-based log ingestion, incremental date tracking |
| Workflow automation | Amazon S3 event notifications, AWS Lambda, AWS Glue jobs |
| Data transformation | AWS Glue, PySpark, validation and standardization |
| Data lake storage | Amazon S3, raw and processed zones, Apache Parquet |
| Data warehousing | Amazon Redshift Serverless, incremental data loading, SQL table design, `COPY` loading |
| Query and analysis | Amazon Athena, SQL |
| Business intelligence | Power BI Project (`.pbip`), semantic modeling, DAX, report design |
| Security and configuration | AWS IAM roles, environment-based configuration, excluded credentials and local artifacts |

## Repository Structure

```text
.
|- data-ingestion/
|  |- support-tickets/              # Database extraction and source schema
|  `- support-logs/                 # Daily log ingestion and tracking
|- data-transformation/
|  |- support-tickets-transformation/
|  `- support-log-transformation/   # ETL workflows
|- data-warehousing-analytics/
|  |- redshift-setup/               # DDL and Redshift loading notebooks
|  `- athena-sql-queries/           # Example analytical SQL
|- careplus ticket insight.SemanticModel/  # Power BI model and DAX measures
|- careplus ticket insight.Report/          # Power BI report definition
|- screenshots/                     # Dashboard previews
`- aws_project_pipeline.svg         # Solution architecture
```

## Getting Started

1. Clone this repository.
2. Copy `.env.example` to `.env` and configure the local database, S3, and Redshift settings. Do not commit `.env`.
3. Configure AWS authentication using an AWS profile or IAM role.
4. Replace placeholder S3 bucket, Redshift endpoint, and IAM role references in notebooks and SQL scripts with resources from your AWS account.
5. Run the ingestion and transformation notebooks, and configure the S3 event notification and Lambda permissions required for automated processing.
6. Create and load the Redshift tables using the resources in `data-warehousing-analytics/redshift-setup/`.
7. Open `careplus ticket insight.pbip` in Power BI Desktop, update the Redshift connection, and refresh the semantic model.

Exact execution steps may vary depending on the AWS resources and configuration used in your environment.

## Privacy and Configuration

This repository is intended for portfolio sharing. Credentials, local environment files, Power BI workspace state, generated Parquet files, and operational log samples are excluded by `.gitignore`. Screenshots and sample SQL use anonymized agent labels. Use only approved, non-sensitive data when adapting the project.

## Notes for Reviewers

CarePlus Ticket Insights is a portfolio implementation based on a realistic support-analytics use case. It demonstrates an end-to-end workflow spanning ingestion, automated transformation, incremental warehouse loading, SQL analytics, and BI reporting.

Infrastructure identifiers in the repository are placeholders and must be replaced with resources from the target AWS account before execution.
