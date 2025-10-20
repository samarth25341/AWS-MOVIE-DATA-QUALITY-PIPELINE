# AWS-MOVIE-DATA-QUALITY-PIPELINE
is an automated data-quality validation and transformation pipeline built entirely on Amazon AWS using Glue, Redshift, S3, CloudWatch, EventBridge, and SNS — with zero code.
The project validates movie datasets from Kaggle, applies rule-based checks, routes successful and failed records automatically, and enables analytics directly through Amazon Athena via the Glue Data Catalog.

AWS Services Used

Amazon S3 – Stores raw input data and failed records
AWS Glue Crawler – Scans S3 and registers metadata in the Glue Data Catalog
AWS Glue Visual ETL Job – Performs no-code transformations and rule-based validation
Conditional Router (Glue Visual Node) – Separates valid and invalid records based on rules
Amazon Redshift – Stores successfully validated data for analytics
AWS Glue Data Catalog – Central schema registry linked to both S3 and Redshift
Amazon Athena – Queries Redshift and S3 data using the Glue Catalog
Amazon CloudWatch – Monitors Glue jobs and captures failure events
Amazon EventBridge – Triggers workflows when CloudWatch detects failures
Amazon SNS – Sends job status notifications

Workflow Description
Data Source
 Movie dataset downloaded from Kaggle and uploaded to Amazon S3 (raw zone).
Schema Registration
 An AWS Glue Crawler scans S3 and registers table metadata in the Glue Data Catalog.
Transformation and Validation
 A Glue Visual ETL Job reads from the Glue Catalog and applies quality rules using the Conditional Router.

Example rule:

IMDB_Rating > 7.5

A new column data_status is added to mark each record as:
"Success" → passes the rule
"Failed" → does not meet the condition

Conditional Routing
 ✅ Success Path:
 Data passing validation is written to Amazon Redshift via a JDBC connection.
 Redshift is connected to the Glue Catalog, making it queryable in Athena.

❌ Failure Path:
 Failed records are logged in CloudWatch.
 EventBridge listens for specific failure events from CloudWatch and routes them to S3.
 These records are stored for later inspection or reprocessing.

Event-Driven Automation

When CloudWatch captures a job failure, EventBridge automatically triggers an event pattern to push details and failed data to a defined S3 target location.
SNS sends notifications about pipeline completion or errors.

Querying & Analytics
Final validated data in Redshift can be queried directly from Athena, leveraging the Glue Data Catalog as the metadata layer.

Key Features

100% no-code data pipeline using AWS Glue Visual ETL

Rule-based data quality validation (IMDB Rating > 7.5)

Adds data_status column for clear pass/fail tracking

Automated failure handling via CloudWatch → EventBridge → S3

JDBC integration between Glue and Redshift

Athena querying for both Redshift and S3 datasets

Event-driven alerts using SNS

Architecture
Kaggle Dataset
      ↓
Amazon S3 (Raw Data)
      ↓
AWS Glue Crawler → AWS Glue Data Catalog
      ↓
AWS Glue Visual ETL Job
      ↓
 ┌────────────────────────────┐
 │ Conditional Router Node    │
 │ Rule: IMDB_Rating > 7.5    │
 └────────────────────────────┘
      ↓
 ┌──────────────┬──────────────┐
 │ Failed Data  │ Successful Data │
 │ (→ CloudWatch │ (→ Amazon Redshift │
 │  → EventBridge │  via JDBC)       │
 │  → S3 Storage) │  ↓               │
 └──────────────┴──────────────┘
       ↓                          ↓
EventBridge → S3 (Failures)   Redshift ↔ Glue Catalog ↔ Athena
       ↓
SNS (Notifications)

Future Enhancements

Add multiple conditional rules and thresholds

Integrate AWS Deequ for advanced data-quality profiling

Use Step Functions for orchestrating end-to-end batch flow

Build a QuickSight dashboard for real-time analytics.

Dataset

Source: Kaggle Movie Dataset

Format: CSV

Storage: Amazon S3
