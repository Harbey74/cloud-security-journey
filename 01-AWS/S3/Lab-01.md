# Lab 01 – S3 Object Operations

## Objective

The objective of this lab was to gain hands-on experience managing objects in Amazon S3 using the AWS CLI.

The lab focused on uploading, listing, downloading, and deleting objects while understanding the IAM permissions required for each operation.

---

## Architecture

```text
                 AWS CLI
                    │
                    ▼
        skyshield-abiodun-2026
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       Upload    Download    Delete
          │         │         │
          └─────────┼─────────┘
                    ▼
               S3 Objects


Resources
S3 Bucket
Bucket Name: skyshield-abiodun-2026
Test Object: lab1.txt
AWS CLI Profile: skyshield

Implementation Steps
-- Created the S3 bucket.
-- Created a local test file.
-- Uploaded the file to the S3 bucket.
-- Listed objects within the bucket.
-- Downloaded the object from the bucket.
-- Deleted the test object.


Permission Requirements

The lab demonstrated that different S3 operations require different IAM permissions.

| Operation              | Required Permission |
| ---------------------- | ------------------- |
| List objects in bucket | `s3:ListBucket`     |
| Upload object          | `s3:PutObject`      |
| Download object        | `s3:GetObject`      |
| Delete object          | `s3:DeleteObject`   |


Permission Validation

The operations were tested using the AWS CLI.

| Test                | Expected | Result |
| ------------------- | -------- | ------ |
| List bucket objects | Allow    | ✅      |
| Upload object       | Allow    | ✅      |
| Download object     | Allow    | ✅      |
| Delete object       | Allow    | ✅      |


Observations
Bucket Listing vs Object Listing

There is an important distinction between listing all buckets and listing objects inside a specific bucket.

Running: aws s3 ls, requests the s3:ListAllMyBuckets permission.

Running:aws s3 ls s3://skyshield-abiodun-2026, requests the s3:ListBucket permission for the specified bucket.

Security Principles Demonstrated
-- Principle of Least Privilege
-- Operation-specific permissions
-- Resource-level access control
-- Default Deny
-- Permission validation


Lessons Learned
-- S3 permissions are granular and operation-specific.
-- s3:ListAllMyBuckets and s3:ListBucket are different permissions.
-- Object-level operations such as upload, download, and delete require their respective    permissions.
-- Having one S3 permission does not automatically grant other S3 permissions.
-- Testing permissions with the AWS CLI provides a practical way to validate an IAM policy.
-- Understanding the exact API action behind an AWS CLI command is important when designing least-privilege IAM policies.
