# IAM Commands

## List IAM Users

```bash
aws iam list-users --profile skyshield
```

## Create IAM User

```bash
aws iam create-user --user-name media-volunteer --profile skyshield
```

## Delete IAM User

```bash
aws iam delete-user --user-name media-volunteer --profile skyshield
```

## List IAM Groups

```bash
aws iam list-groups --profile skyshield
```

## Create IAM Group

```bash
aws iam create-group --group-name Media-Team --profile skyshield
```

## Delete IAM Group

```bash
aws iam delete-group --group-name Media-Team --profile skyshield
```

## Add User to Group

```bash
aws iam add-user-to-group \
--group-name Media-Team \
--user-name media-volunteer \
--profile skyshield
```

## Remove User from Group

```bash
aws iam remove-user-from-group \
--group-name Media-Team \
--user-name media-volunteer \
--profile skyshield
```

## View Group Members

```bash
aws iam get-group \
--group-name Media-Team \
--profile skyshield
```

## Create Customer Managed Policy

```bash
aws iam create-policy \
--policy-name MediaTeamS3Access \
--policy-document file://JSON/media-team-policy.json \
--profile skyshield
```

## List Customer Managed Policies

```bash
aws iam list-policies \
--scope Local \
--profile skyshield
```

## Get Policy Details

```bash
aws iam get-policy \
--policy-arn <policy-arn> \
--profile skyshield
```

## Attach Policy to Group

```bash
aws iam attach-group-policy \
--group-name Media-Team \
--policy-arn <policy-arn> \
--profile skyshield
```

## List Attached Group Policies

```bash
aws iam list-attached-group-policies \
--group-name Media-Team \
--profile skyshield
```

## Create Access Key

```bash
aws iam create-access-key \
--user-name media-volunteer \
--profile skyshield
```

## Delete Access Key

```bash
aws iam delete-access-key \
--user-name media-volunteer \
--access-key-id <AccessKeyId> \
--profile skyshield
```

## Configure AWS CLI Profile

```bash
aws configure --profile media-volunteer
```

## Verify Current Identity

```bash
aws sts get-caller-identity --profile media-volunteer
```

## Test Bucket Listing

```bash
aws s3 ls s3://skyshield-abiodun-2026 --profile media-volunteer
```

## Upload Object

```bash
aws s3 cp volunteer-test.txt s3://skyshield-abiodun-2026 --profile media-volunteer
```

## Download Object

```bash
aws s3 cp s3://skyshield-abiodun-2026/volunteer-test.txt volunteer-download.txt --profile media-volunteer
```

## Delete Object (Expected to Fail)

```bash
aws s3 rm s3://skyshield-abiodun-2026/volunteer-test.txt --profile media-volunteer
```