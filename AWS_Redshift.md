
# AWS Redshift

* Redshift is based on PostgreSQL, but it’s not used for OLTP
* It’s OLAP – online analytical processing (analytics and data warehousing)
* 10x better performance than other data warehouses, scale to PBs of data
* Columnar storage of data (instead of row based)
* Massively Parallel Query Execution (MPP)
* Pay as you go based on the instances provisioned
* Has a SQL interface for performing the queries
* BI tools such as AWS Quicksight or Tableau integrate with it
* Data is loaded from S3, DynamoDB, DMS, other DBs.
* From 1 node to 128 nodes, up to 128 TB of space per node
* Leader node: for query planning, results aggregation
* Compute node: for performing the queries, send results to leader
* Redshift Spectrum: perform queries directly against S3 (no need to load)
* Backup & Restore, Security VPC / IAM / KMS, Monitoring
* Redshift Enhanced VPC Routing: COPY / UNLOAD goes through VPC

# Redshift – Snapshots & DR

* Redshift has no “Multi-AZ” mode
* Snapshots are point-in-time backups of a cluster, stored internally in S3
* Snapshots are incremental (only what has changed is saved)
* You can restore a snapshot into a new cluster
* Automated: every 8 hours, every 5 GB, or on a schedule. Set retention
* Manual: snapshot is retained until you delete it
* You can configure Amazon Redshift to automatically copy snapshots (automated or manual) of a cluster to another AWS Region

# Loading data into Redshift

![](https://raw.githubusercontent.com/sudheera96/Orchestrate-Redshift-ETL-using-AWS-Glue-and-Step-Functions/main/images/redshift.jpg)

# Redshift Spectrum

* Query data that is already in S3 without loading it
* Must have a Redshift cluster available to start the query
* The query is then submitted to thousands of Redshift Spectrum nodes
