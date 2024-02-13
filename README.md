# AWS-Snowflake Integration

The AWS-Snowflake integration project harnesses the capabilities of AWS S3 buckets, IAM policies, and roles to seamlessly integrate with Snowflake's cloud data platform. This enables secure and efficient data transfer, transformation, and loading processes, empowering users to leverage the scalability and reliability of AWS alongside Snowflake's advanced analytics capabilities.

## AWS Components Used
- **S3 Bucket:** A simple Storage Service (S3) bucket named `demo-08022024` has been created in the selected AWS region to store data securely.

[Link to AWS S3 Bucket](<s3://demo-08022024/family.csv>)

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

