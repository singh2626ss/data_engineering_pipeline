# data_engineering_pipeline 
YouTube Trending Data Engineering Pipeline End-to-end AWS data engineering project to ingest, transform, and analyze trending YouTube video data using S3, Glue, Lambda, Athena, and QuickSight. This pipeline handles raw CSV/JSON data from Kaggle, performs ETL into Parquet, and delivers insights via an interactive BI dashboard
YouTube Trending Data Engineering Project
🔍 Overview
This project is a full-stack data engineering pipeline built on AWS to ingest, process, store, and analyze YouTube trending video data. Inspired by Darshil Parmar’s architecture, I implemented an end-to-end pipeline that leverages the AWS ecosystem — with S3 as the data lake, Glue and Lambda for ETL, Athena for querying, and QuickSight for BI visualization.

The focus was to simulate a real-world scenario of handling structured and semi-structured data at scale, making it a strong portfolio piece for cloud-based data engineering.

🎯 Project Goals
Data Ingestion: Collect structured and semi-structured YouTube trending video data from Kaggle using regional CSV and JSON metadata.

ETL: Design an automated ETL process using Glue jobs and Lambda functions to clean, normalize, and convert raw CSV/JSON data into columnar Parquet format.

Data Lake Formation: Organize the lake into Landing, Cleansed, and Analytics layers within S3 for clear data lifecycle separation.

Scalability: Use serverless components (Lambda, Glue, Athena) to ensure the system can scale with minimal manual intervention.

Cloud-native: Fully built on AWS to simulate a production-grade, serverless, low-maintenance architecture.

Reporting: Use QuickSight for storytelling and insights — from engagement by video category to temporal spikes in viewership.

🧰 Services Used
Amazon S3: Central data lake to store raw, cleansed, and analytics-ready data.

AWS Glue: Crawler to catalog CSV/JSON, Glue jobs for ETL transformation and mapping schema.

AWS Lambda: JSON normalization + Parquet conversion function with catalog integration via awswrangler.

AWS Athena: SQL querying on top of cataloged S3 Parquet data using partition optimization.

QuickSight: Dashboard to display KPIs — top categories, country-level popularity, user feedback trends, etc.

AWS IAM: Managed roles for service-to-service access — Lambda, Glue, and QuickSight permissions.

AWS Step Functions (optional in the reference): Visual workflow for orchestration (not used in this implementation but included in the architectural vision).

📂 Dataset
Source: Kaggle - YouTube Trending Videos

Regional CSV files per country (CA, GB, US).

Metadata mapping in separate JSON for each region.

Bottlenecks & How I Resolved Them
❌ Lambda Timeout / Memory Limit

Problem: Initially hit a 50-second timeout due to large JSON normalization in Lambda.

Fix: Increased memory to 256MB and ensured input files were reasonably sized. Coalesced final frame in Glue before writing.

❌ AWS Wrangler S3 → Glue Path Conflict

Problem: Lambda tried writing Parquet to a different path than Glue’s registered catalog, causing path mismatch errors.

Fix: Registered a new Glue table pointing to the correct cleansed path. Made sure Glue and S3 URIs aligned perfectly.

❌ QuickSight Not Detecting Glue Tables

Problem: “No Tables Found” error in QuickSight when connecting via Athena.

Fix: Region mismatch! QuickSight was set to N. Virginia, while Glue/Athena resources were in Ohio. Switched QuickSight region.

❌ Missing Data Catalog in QuickSight Permissions

Problem: Glue catalog wasn’t discoverable.

Fix: Turns out Glue doesn't appear as an explicit checkbox in QuickSight — it's accessed through Athena + S3 bucket permission. Once those were enabled, tables showed up.

❌ Parquet Schema Mismatch in QuickSight

Problem: bigint in Athena appeared as long or even string in QuickSight.

Fix: Ensured data types were enforced during mapping in Glue ApplyMapping. Also manually cleaned types inside QuickSight's “Change Data Type” menu.

📈 Visualizations Created
📊 Top Categories by Likes: Stacked bar chart showing which content types generate highest engagement.

🌎 Region-Wise Dataset Distribution: Pie chart of dataset spread across US, CA, GB.

📈 Trending Over Time: Time series line chart showing how popularity fluctuates across regions.

😡 Negative Feedback vs Interaction: Stacked bars comparing dislikes and comment counts per region.

👑 Most Viewed Video (KPI): Single value indicator for the peak view count in the dataset.

📝 How to Reproduce
Clone project repo (if applicable).

Upload Kaggle data into your S3 bucket under a raw_statistics/ path.

Create a Glue database and run a crawler on the raw zone.

Deploy Lambda function or Glue Job to transform and output Parquet to cleansed/.

Run another crawler on cleansed/ and make sure Athena can see the new table.

Connect Athena to QuickSight and build the dashboard.

📍 Final Thoughts
This project gave me hands-on experience across the full AWS data stack and highlighted how even small misconfigurations (like mismatched regions or URI paths) can halt your pipeline.

I plan to extend this by:

Adding Step Functions for orchestration.

Scheduling regular updates via CloudWatch + Lambda.

Creating an ML pipeline using SageMaker based on engagement prediction.
link to the dashboard (dashboard still in progress) :- https://us-east-2.quicksight.aws.amazon.com/sn/accounts/522113393514/dashboards/ab497dbd-8807-4d51-bb31-87feb964160c?directory_alias=Saransh-Singh



Fields: video_id, title, channel_title, publish_time, views, likes, dislikes, comment_count, tags, etc.

Category mapping is in JSON — requiring an additional transformation join during ETL.

