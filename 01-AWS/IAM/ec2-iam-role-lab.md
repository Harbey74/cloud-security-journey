# EC2 IAM Role and Temporary Credentials Lab

## Objective

The objective of this lab was to demonstrate how an EC2 instance can securely access an S3 bucket using an IAM Role and temporary credentials instead of long-lived IAM User access keys.

The lab also demonstrated the difference between a role's trust policy and permission policy, the purpose of an EC2 Instance Profile, AWS STS temporary credentials, IMDSv2, and least-privilege authorization.

## Architecture

```text
EC2 Instance
     │
     ▼
Instance Profile
     │
     ▼
ChurchEC2S3Role
     │
     ├── Trust Policy
     │      └── EC2 trusted to assume role
     │
     └── Permission Policy
            ├── s3:ListBucket
            ├── s3:GetObject
            └── s3:PutObject
                    │
                    ▼
          skyshield-abiodun-2026
```

## Implementation

### 1. Created the IAM Role

The role created was:

```text
ChurchEC2S3Role
```

Its trust policy allowed the EC2 service to assume the role:

```json
{
  "Effect": "Allow",
  "Principal": {
    "Service": "ec2.amazonaws.com"
  },
  "Action": "sts:AssumeRole"
}
```

### 2. Created the Permission Policy

A separate permission policy was created for the role.

The policy allowed:

* `s3:ListBucket`
* `s3:GetObject`
* `s3:PutObject`

The permissions were restricted to the `skyshield-abiodun-2026` bucket and its objects.

`s3:DeleteObject` was intentionally excluded because deletion was not required for the workload.

### 3. Created the EC2 Instance Profile

The EC2 console did not initially show the role because the role had been created using the CLI without an Instance Profile.

An Instance Profile was therefore created and the role was added to it.

```text
ChurchEC2S3Role
      ↓
Instance Profile
      ↓
EC2
```

### 4. Launched Amazon Linux 2023

An Amazon Linux 2023 EC2 instance was launched with the `ChurchEC2S3Role` Instance Profile attached.

SSH access was restricted through the Security Group.

### 5. Verified the Assumed Role

Inside the EC2 instance:

```bash
aws sts get-caller-identity
```

returned an identity in the form:

```text
arn:aws:sts::<account-id>:assumed-role/ChurchEC2S3Role/<session>
```

This confirmed that the instance was operating using the IAM Role rather than an IAM User.

### 6. Verified Credential Source

Running:

```bash
aws configure list
```

showed:

```text
access_key : ******** : iam-role
secret_key : ******** : iam-role
region     : eu-west-1 : imds
```

This demonstrated that the AWS CLI was obtaining credentials from the EC2 IAM Role rather than from a credentials file.

### 7. Verified S3 Permissions

The EC2 instance successfully listed the S3 bucket:

```bash
aws s3 ls s3://skyshield-abiodun-2026
```

It also successfully uploaded an object:

```bash
aws s3 cp ec2-role-test.txt s3://skyshield-abiodun-2026
```

### 8. Tested Least Privilege

An attempt was made to delete the uploaded object:

```bash
aws s3 rm s3://skyshield-abiodun-2026/ec2-role-test.txt
```

The request failed with `AccessDenied` because the role did not have:

```text
s3:DeleteObject
```

This demonstrated that the role was operating according to the intended least-privilege policy.

### 9. Verified IMDSv2

The EC2 Instance Metadata Service was queried using an IMDSv2 token.

The metadata endpoint identified:

```text
ChurchEC2S3Role
```

and returned temporary credential metadata containing an expiration time.

The credentials were not stored in:

```text
~/.aws/credentials
```

This demonstrated how EC2 workloads can obtain temporary credentials dynamically without manually storing long-lived access keys.

## Lab Outcome

The lab successfully demonstrated the complete EC2 IAM Role authentication and authorization flow:

```text
EC2
 ↓
Instance Profile
 ↓
Trust Policy
 ↓
STS
 ↓
Temporary Credentials
 ↓
IMDSv2
 ↓
AWS CLI/Application
 ↓
Permission Policy
 ↓
S3
```

The implementation confirmed that IAM Roles provide a safer and more scalable approach for AWS workloads compared with storing long-lived access keys.

## Security Lessons

1. Trust and permission policies serve different purposes.
2. Authentication and authorization are separate stages.
3. Successfully assuming a role does not guarantee resource access.
4. EC2 workloads should use IAM Roles instead of embedded access keys.
5. Temporary credentials reduce the risk associated with long-lived credentials.
6. IMDSv2 provides an additional security control for instance metadata access.
7. Least privilege should restrict both actions and resources.
8. Excluding unnecessary permissions reduces the blast radius of a compromised workload.
9. Instance Profiles provide the mechanism for associating IAM Roles with EC2 instances.
10. IAM Roles provide centralized and scalable permission management for workloads.
