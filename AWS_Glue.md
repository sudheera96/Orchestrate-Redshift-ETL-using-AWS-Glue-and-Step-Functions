# AWS Glue

*  Managed extract, transform, and load (ETL) service
*  Useful to prepare and transform data for analytics
*  Fully serverless service

![](https://raw.githubusercontent.com/sudheera96/Orchestrate-Redshift-ETL-using-AWS-Glue-and-Step-Functions/main/images/aws%20glue.jpg)

# Glue Data Catalog

* Glue Data Catalog: catalog of datasets

![](https://raw.githubusercontent.com/sudheera96/Orchestrate-Redshift-ETL-using-AWS-Glue-and-Step-Functions/main/images/aws%20data%20catlog.jpg)

# Glue Job Creation

1. Go to search >> glue >> connection >> Add connection (has to connect with redshift) >> name as "myredshiftconnection" >> connection type :AWS Redshift >> connection access >> cluster details (as only one cluster is running we add that cluster) >> review the steps >> finish
2. Now select the created connection >> test connection >> IAM role ( we have to give the glue to access the redshift) >> create new IAM role >> create role >> search glue >> select AWS glue service role (Policy for AWS Glue service role which allows access to related services including EC2, S3, and Cloudwatch Logs) >> Along with it create other policy for connecting to s3 and connecting to secret manager >> copy and paste the provide policy of glue with providing secret manager ARN connection id >> next tag >> next review >> my glue policy >> create policy 
3. Refresh policy >> select created policy >> aws glue service role >> next >> name: mygluerole >> create
4. come back to the attaching IAM role to glue >> refresh >> choose mygluerole >> testconnection
5. Crete glue job (as we having table and scriptsa ready) >> click on job (legacy) >> 
