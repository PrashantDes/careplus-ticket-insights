# CarePlus Ticket Insights

CarePlus is a business process outsourcing (BPO) company that handles customer support across multiple channels. This project brings support ticket data and operational support logs together in a centralized AWS analytics pipeline and presents the resulting insights in a Power BI dashboard.

## Problem statement

Support information was spread across structured and unstructured sources, including CSV and Excel files, databases, and application logs. Teams relied on multiple Excel dashboards and ad hoc SQL queries, making it difficult to maintain a consistent view of ticket performance and the systems supporting customer interactions.

## Solution

The project establishes a shared data pipeline and analytics layer for support tickets and support logs. AWS services ingest and transform the data into analytics-ready storage; Amazon Redshift provides a central warehouse for reporting, while Athena supports SQL analysis. A Power BI semantic model and dashboard bring customer-support and operational measures together in one place.

### Architecture

<p align="center">
  <img src="aws_project_pipeline.svg" alt="CarePlus AWS data pipeline architecture" width="100%">
</p>

The pipeline follows these stages:

1. **Ingest:** Ticket records are read from the support database, and support log files are collected as source data.
2. **Store raw data:** Amazon S3 holds incoming data in raw prefixes.
3. **Transform:** S3 events invoke AWS Lambda, which starts AWS Glue jobs to clean and transform the data.
4. **Store processed data:** Transformed datasets are written to S3 in Parquet format.
5. **Analyze and warehouse:** Amazon Redshift Serverless loads the processed ticket and log data for centralized analytics. Amazon Athena provides SQL-based, ad hoc analysis.
6. **Visualize:** Power BI connects to Redshift and presents a unified view of ticket and support-log performance.

AWS IAM roles are used to grant services the access needed to move and query data.

## Power BI dashboard

The published Power BI report is hosted externally and is not changed by this repository. Its current version may display agent names, so the live link is intentionally omitted until the anonymized model is refreshed and republished.

The report contains two views:

- **Support tickets:** Ticket volume and status by channel, agent workload, resolution time, interaction count, and agent resolution summaries.
- **Support logs:** Log volume, CPU utilization, response time, log levels, user agents, and ticket activity.

### Support tickets

<p align="center">
  <img src="screenshots/Screenshot%202026-10-29%20205104.png" alt="CarePlus support tickets dashboard" width="100%">
</p>

### Support logs

<p align="center">
  <img src="screenshots/Screenshot%202026-10-29%20205118.png" alt="CarePlus support logs dashboard" width="100%">
</p>

## Data model and measures

The Power BI model combines:

- **`support_tickets`** — ticket ID, created and resolved timestamps, agent, priority, issue category, channel, status, and interaction count.
- **`support_logs`** — event timestamp, log level, component, ticket and session IDs, response time, CPU, error indicator, user agent, and log details.
- **Shared dimensions** — ticket and date dimensions connect ticket and log activity for consistent filtering and analysis.

Key dashboard measures include total, resolved, open, and escalated tickets; average resolution time; average interactions; total agents; total logs; logged tickets; average CPU; and average response time.

## Technology stack

| Area | Technologies |
|---|---|
| Ingestion and storage | Amazon S3 |
| Orchestration and transformation | AWS Lambda, AWS Glue |
| Data format | Apache Parquet |
| Data warehouse | Amazon Redshift Serverless |
| Ad hoc SQL | Amazon Athena |
| Access control | AWS IAM |
| Reporting and semantic model | Microsoft Power BI |

## Repository contents

- `data-ingestion/` — support ticket and support log ingestion notebooks and source data.
- `data-transformation/` — ticket and log transformation workflows.
- `data-warehousing-analytics/` — Redshift table setup and Athena SQL examples.
- `careplus ticket insight.SemanticModel/` — Power BI semantic model and measures.
- `careplus ticket insight.Report/` — Power BI report definition.
- `aws_project_pipeline.svg` — AWS pipeline architecture diagram.
- `screenshots/` — dashboard screenshots embedded above.

## Notes

The Power BI link is a published report. Access to AWS resources, source data, and the editable Power BI project may require appropriate permissions and configuration.

## Local configuration and privacy

- Never commit credentials, `.env` files, local Power BI state, or private source data. The repository ignore rules exclude these local files.
- Copy `.env.example` to `.env` and set your own database and S3 configuration before running ingestion locally. Use an AWS profile or an IAM role for AWS credentials; do not put access keys in notebooks.
- Replace the placeholder Redshift endpoint and IAM role in the project configuration with resources in your own AWS account.
- The ticket screenshot and SQL seed data use anonymized agent labels; local operational log samples are excluded from Git. Do not upload customer data or personally identifiable information.

The Power BI semantic model contains a placeholder Redshift endpoint for safe sharing and replaces source agent names with stable labels such as `Agent 01` when refreshed. Configure the endpoint and refresh the model before publishing a new dashboard link.
