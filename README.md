# AWS-Snowflake Integration

The AWS-Snowflake integration project harnesses the capabilities of AWS S3 buckets, IAM policies, and roles to seamlessly integrate with Snowflake's cloud data platform. This enables secure and efficient data transfer, transformation, and loading processes, empowering users to leverage the scalability and reliability of AWS alongside Snowflake's advanced analytics capabilities.

## AWS Components Used
- **S3 Bucket:** A simple Storage Service (S3) bucket named `demo-08022024` has been created in the selected AWS region to store data securely.

[Link to AWS S3 Bucket](<s3://demo-08022024>)

- **IAM Policies and Roles:** IAM policies and roles have been configured to grant necessary permissions for accessing the S3 bucket and other AWS services required for data integration.

**Policy JSON:**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:DeleteObject",
                "s3:DeleteObjectVersion"
            ],
            "Resource": "arn:aws:s3:::demo-08022024/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::demo-08022024",
            "Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "*"
                    ]
                }
            }
        }
    ]
}
```

After configuring the IAM policy, proceed with the next steps to set up Snowflake integration. Snowflake is a powerful cloud data platform that enables seamless data warehousing and analytics capabilities.

## Setting Up Snowflake Integration
To integrate AWS S3 with Snowflake, follow these steps:

Create a new worksheet in your Snowflake dashboard.

Execute the provided SQL commands to set up the database, schema, and external stage for AWS S3 integration.

-- Create a database and schema
```sql
CREATE DATABASE DEMO;
CREATE SCHEMA DEMO_08022024;
```

-- Create an external stage for AWS S3 integration
```sql
CREATE OR REPLACE STORAGE integration aws_s3_integartion
type = external_stage
storage_provider='S3'
enabled = true
storage_aws_role_arn='arn:aws:iam::471112549899:role/demo_08022024'
storage_allowed_locations = ('s3://demo-08022024');
```

## AWS Trusted Relationship Configuration

To configure the AWS trusted relationship, follow these steps:

1.Navigate to the IAM dashboard in AWS.
2.Open the roles section and select the role associated with your Snowflake integration (demo_08022024).
3.Go to the "Trust relationships" tab and click "Edit trust relationship".
4.Replace the existing trust relationship JSON with the provided options.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::975050337450:user/externalstages/cihpv20000"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "sts:ExternalId": "YL79576_SFCRole=2_xlDjvd2P9z2kXdQMRah/bsr3tYM="
                }
            }
        }
    ]
}
```
## Executing Snowflake SQL Code

After setting up AWS components and configuring the trusted relationship, run the following SQL commands in Snowflake:

-- Grant usage on integration to accountadmin role
```sql
GRANT USAGE ON INTEGRATION aws_s3_integration TO ROLE accountadmin;
```

-- Define file format for CSV files
```sql
CREATE OR REPLACE FILE FORMAT demo_format
  TYPE = 'CSV'
  FIELD_DELIMITER = ','
  SKIP_HEADER = 1;
```

-- Create stage for AWS S3 integration
```sql
CREATE OR REPLACE STAGE demo_aws_stage
  STORAGE_INTEGRATION = aws_s3_integration
  FILE_FORMAT = demo_format
  URL = 's3://demo-08022024';
```

-- List files in the stage
```sql
LIST @demo_aws_stage;
```

-- Optionally, remove specific files from the stage
```sql
REMOVE @demo_aws_stage/customers-100.csv;
```

-- Create temporary table to hold data
```sql
CREATE OR REPLACE TEMPORARY TABLE demo_family_info (
  id INTEGER,
  name STRING,
  city STRING,
  phone STRING
);
```

-- Copy data from stage into temporary table
```sql
COPY INTO demo_family_info
FROM @demo_aws_stage/family.csv
FILE_FORMAT = (FORMAT_NAME = demo_format)
ON_ERROR = 'SKIP_FILE';
```
-- Alternatively, use COPY INTO with ON_ERROR option
```sql
COPY INTO demo_family_info
FROM @demo_aws_stage/family.csv
FILE_FORMAT = (FORMAT_NAME = demo_format)
ON_ERROR = 'CONTINUE';
```

-- Query data in temporary table
```sql
SELECT * FROM demo_family_info;
```

## Sample Data File in S3 Bucket

A sample CSV file named family.csv resides in the AWS S3 bucket demo-08022024. It contains the following data



![image](https://github.com/saliyamraju/AWS-Snowflake--Integartion/assets/58658157/81cb558a-b9a7-42b2-a6fc-8f69d881071a)







 

