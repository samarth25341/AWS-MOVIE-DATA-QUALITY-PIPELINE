# AWS-MOVIE-DATA-QUALITY-PIPELINE
is a no-code AWS data pipeline using Glue, Redshift, S3, CloudWatch, EventBridge, and SNS. It validates movie datasets, routes success to Redshift (queryable via Athena) and failures to S3 via EventBridge, with automated monitoring and alerts
