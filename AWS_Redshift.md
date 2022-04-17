
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

# Redshift Cluster Creation

Read the data from s3 using redshift spectrum and load into the redshift and run some quieries on redshift and extract the tranformed data from redshift and put it into s3. So we redshift as datawarehouse where we can run select quieries. We create redshift and put inthe private subnet because it will hold confidential data

1.  Go to redshift from search bar and ensure to create redshift in the same region of your VPC creation.
2.  Before that in the menu go to the config and select manage subnet groups.
3.  Now give cluster subnet group name and description as 'myreviewsubnetgroup'
4.  Attach VPC and two private subnets created in different AZ and click on create cluster subnet group.
5.  Now go into the clusters from menu and click on create new cluster.
6.  Now give name as 'myproject cluster'. If your free tire is over click on production ad select cluster size and node if not for free it will automatically provide cluster size as dc2.large | 1 node.
7.  Now enter the admin user and password.

# Creation of IAM roles

9.  Now cluster require permissions for that we need to create IAM roles
10.  Now in search bar go to IAM >> roles >> AWS services >> Redshift >> Redshift customizable >> next >> create policy >> JSON >> copy and paste provide policy.
11. So in policy we provide permissions like redshift can access s3 and glue in JSON format. So the redshift can access only buckets which you mentioned in resource of JSON. For that create your buckets with the same names provided in the JSON or replace the JSON with your created bucket names.( this bucket is about loading the date from redshift to s3).For s3 to redshift amazon already provided the bucket name.
12. Click on next tags >> review>> give policy name as "myprojectredshiftpolicy" >> create policy 
13. Now go to roles >> select created policy  "myprojectredshiftpolicy" from list >> next >> give role name as "myproject_redshiftrole" >> create role

# Back to redshift screen for creation

15. Back to redshift screen >> action >> manage IAM roles >> cluster permissions >> Associated IAM  role> select myproject_redshiftrole and associate
16. Edit network settings >> (if you created cluster from free trail then aditional configurations won't available)  >> trun off use defaults >> conect created VPC >> rest keep as default >> AZ selection no require as it being in the private subnet
17. In database give name as review and keep rest all as default >> click on create cluster
18. Try to conneect with redshift and run some quieries i.e we need to create tables which will be loaded in next step like glue jobs

# Creation of Secret Manager

20. Go to cluster >> check created cluster availability >> go back to the editor page >> connect to database >> creaate new connection >> AWS secret manger ( always use this for storing the credentials rather than sharing to every one) >> store new secret >> database name>> database user >> password >> connect to cluster
21. Go the search >> give secret manager and check created new secret >> click on the create secret "redshiftqueryeditor-admin-myredshiftsecret" >> Retreview the secret value to look into details.
22. But cluster still in my private subnet no one will be able to access, unless I grant the IP address of the other person to login into the cluster.
23. So we can use the "redshiftqueryeditor-admin-myredshiftsecret" name rather than hard coring the credentials

