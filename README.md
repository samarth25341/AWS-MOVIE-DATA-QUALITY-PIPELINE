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

<img width="750" height="432" alt="image" src="https://github.com/user-attachments/assets/6c3de516-c50c-46a7-b998-e84d3dff278c" />

<img width="836" height="423" alt="image" src="https://github.com/user-attachments/assets/5b29c13c-2aab-431d-b5a7-cfda966dfb92" />

<img width="839" height="432" alt="image" src="https://github.com/user-attachments/assets/3c1fd7a5-f4a3-459e-b20c-a33f6d469e89" />

<img width="838" height="431" alt="image" src="https://github.com/user-attachments/assets/fe90733b-3212-4ca9-954f-0add0e248cbd" />

<img width="799" height="433" alt="image" src="https://github.com/user-attachments/assets/b5f0b8a6-ce7d-450c-8d38-97cd77075889" />

<img width="534" height="503" alt="image" src="https://github.com/user-attachments/assets/64d13950-2389-486e-8028-a9b4cafdaf80" />

<img width="645" height="489" alt="image" src="https://github.com/user-attachments/assets/bb9b7bdd-aa3f-4cf1-a742-53f4f575110d" />

<img width="744" height="478" alt="image" src="https://github.com/user-attachments/assets/64743507-5f67-4b30-9fad-5ca8f3582647" />

<img width="814" height="450" alt="image" src="https://github.com/user-attachments/assets/d4fc366c-36c4-4dd3-a410-d696fe35fe62" />

<img width="820" height="441" alt="image" src="https://github.com/user-attachments/assets/62df02d6-8534-45a4-85b4-021d9bcc930b" />










