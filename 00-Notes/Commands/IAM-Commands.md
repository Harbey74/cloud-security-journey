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

# IAM Commands

## 1. IAM Role Management

### Create an IAM Role

```bash
aws iam create-role --role-name ChurchEC2S3Role --assume-role-policy-document file://01-AWS/JSON/ec2-trust-policy.json --profile skyshield
```

### View an IAM Role

```bash
aws iam get-role --role-name ChurchEC2S3Role --profile skyshield
```

### List IAM Roles

```bash
aws iam list-roles --profile skyshield
```

### Delete an IAM Role

> Use only after removing attached policies and the role from any instance profile.

```bash
aws iam delete-role --role-name ChurchEC2S3Role --profile skyshield
```

---

## 2. IAM Policy Management

### Create a Customer-Managed IAM Policy

```bash
aws iam create-policy --policy-name MediaTeamS3Access --policy-document file://JSON/media-team-policy.json --profile skyshield
```

### List Customer-Managed Policies

```bash
aws iam list-policies --scope Local --profile skyshield
```

### Get Policy Metadata

```bash
aws iam get-policy --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/MediaTeamS3Access --profile skyshield
```

### List Policy Versions

```bash
aws iam list-policy-versions --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/MediaTeamS3Access --profile skyshield
```

### Get a Specific Policy Version

```bash
aws iam get-policy-version --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/MediaTeamS3Access --version-id v1 --profile skyshield
```

### Delete a Customer-Managed Policy

```bash
aws iam delete-policy --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/MediaTeamS3Access --profile skyshield
```

---

## 3. Attaching Policies to Roles

### Attach a Managed Policy to a Role

```bash
aws iam attach-role-policy \
  --role-name ChurchEC2S3Role \
  --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/MediaTeamS3Access \
  --profile skyshield
```

### List Policies Attached to a Role

```bash
aws iam list-attached-role-policies \
  --role-name ChurchEC2S3Role \
  --profile skyshield
```

### Detach a Policy from a Role

```bash
aws iam detach-role-policy \
  --role-name ChurchEC2S3Role \
  --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/MediaTeamS3Access \
  --profile skyshield
```

---

# 4. IAM Group Management

### Create a Group

```bash
aws iam create-group --group-name Media-Team --profile skyshield
```

### List Groups

```bash
aws iam list-groups --profile skyshield
```

### Get Group Details and Members

```bash
aws iam get-group --group-name Media-Team --profile skyshield
```

### Add a User to a Group

```bash
aws iam add-user-to-group \
  --group-name Media-Team \
  --user-name media-volunteer \
  --profile skyshield
```

### List Policies Attached to a Group

```bash
aws iam list-attached-group-policies \
  --group-name Media-Team \
  --profile skyshield
```

### Attach a Policy to a Group

```bash
aws iam attach-group-policy \
  --group-name Media-Team \
  --policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/MediaTeamS3Access \
  --profile skyshield
```

### Remove a User from a Group

```bash
aws iam remove-user-from-group \
  --group-name Media-Team \
  --user-name media-volunteer \
  --profile skyshield
```

---

# 5. IAM User Management

### Create an IAM User

```bash
aws iam create-user --user-name media-volunteer --profile skyshield
```

### List IAM Users

```bash
aws iam list-users --profile skyshield
```

### Get User Details

```bash
aws iam get-user --user-name media-volunteer --profile skyshield
```

### List Groups a User Belongs To

```bash
aws iam list-groups-for-user \
  --user-name media-volunteer \
  --profile skyshield
```

### Create an Access Key

```bash
aws iam create-access-key --user-name media-volunteer --profile skyshield
```

> Store the secret access key securely. Never commit access keys to GitHub.

---

# 6. IAM Instance Profile Management

EC2 uses an **Instance Profile** to associate an IAM Role with an EC2 instance.

### Create an Instance Profile

```bash
aws iam create-instance-profile \
  --instance-profile-name ChurchEC2S3Role \
  --profile skyshield
```

### Add the Role to the Instance Profile

```bash
aws iam add-role-to-instance-profile \
  --instance-profile-name ChurchEC2S3Role \
  --role-name ChurchEC2S3Role \
  --profile skyshield
```

### View an Instance Profile

```bash
aws iam get-instance-profile \
  --instance-profile-name ChurchEC2S3Role \
  --profile skyshield
```

### List Instance Profiles

```bash
aws iam list-instance-profiles --profile skyshield
```

### Remove a Role from an Instance Profile

```bash
aws iam remove-role-from-instance-profile \
  --instance-profile-name ChurchEC2S3Role \
  --role-name ChurchEC2S3Role \
  --profile skyshield
```

### Delete an Instance Profile

```bash
aws iam delete-instance-profile \
  --instance-profile-name ChurchEC2S3Role \
  --profile skyshield
```

---

# 7. EC2 IAM Role Verification

These commands were executed **inside the Amazon Linux 2023 EC2 instance**.

### Check the Current AWS Identity

```bash
aws sts get-caller-identity
```

Expected identity format:

```text
arn:aws:sts::<ACCOUNT-ID>:assumed-role/ChurchEC2S3Role/<SESSION-NAME>
```

### Check AWS CLI Credential Source

```bash
aws configure list
```

When using the EC2 IAM Role, the output should indicate:

```text
access_key : ******** : iam-role
secret_key : ******** : iam-role
region     : eu-west-1 : imds
```

---

# 8. STS Commands

### Get Current Caller Identity

```bash
aws sts get-caller-identity
```

This is useful for determining whether the current CLI session is using:

* an IAM User
* an assumed IAM Role
* an EC2 Instance Role
* another AWS identity

---

# 9. IMDSv2 Commands

These commands were executed from inside the EC2 instance.

### Request an IMDSv2 Token

```bash
TOKEN=$(curl -X PUT \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600" \
  http://169.254.169.254/latest/api/token)
```

### Retrieve the IAM Role Name

```bash
curl -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

Expected result:

```text
ChurchEC2S3Role
```

### Retrieve Temporary Role Credentials

```bash
curl -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/iam/security-credentials/ChurchEC2S3Role
```

The response contains:

```text
AccessKeyId
SecretAccessKey
Token
Expiration
```

These are temporary credentials automatically provided to the EC2 workload.

---

# 10. EC2-to-S3 Permission Testing

### List Objects in the S3 Bucket

```bash
aws s3 ls s3://skyshield-abiodun-2026
```

### Create a Test File

```bash
echo "Hello from EC2 IAM Role" > ec2-role-test.txt
```

### Upload an Object

```bash
aws s3 cp ec2-role-test.txt s3://skyshield-abiodun-2026
```

### Download an Object

```bash
aws s3 cp \
  s3://skyshield-abiodun-2026/ec2-role-test.txt \
  ec2-role-test-download.txt
```

### Attempt to Delete an Object

```bash
aws s3 rm s3://skyshield-abiodun-2026/ec2-role-test.txt
```

In our lab this operation returned `AccessDenied` because the IAM Role did not have:

```text
s3:DeleteObject
```

This demonstrated the Principle of Least Privilege and IAM's implicit deny behavior.

---

# 11. Useful IAM Inspection Commands

### List Policies Attached to a User

```bash
aws iam list-attached-user-policies \
  --user-name media-volunteer \
  --profile skyshield
```

### List Inline Policies for a User

```bash
aws iam list-user-policies \
  --user-name media-volunteer \
  --profile skyshield
```

### List Inline Policies for a Group

```bash
aws iam list-group-policies \
  --group-name Media-Team \
  --profile skyshield
```

### List Inline Policies for a Role

```bash
aws iam list-role-policies \
  --role-name ChurchEC2S3Role \
  --profile skyshield
```

### List Groups for a User

```bash
aws iam list-groups-for-user \
  --user-name media-volunteer \
  --profile skyshield
```

---

# 12. Common AWS CLI IAM Mistakes Encountered

### Incorrect

```bash
aws iam list-group
```

### Correct

```bash
aws iam list-groups
```

AWS CLI commands must use the exact operation name.

---

### Incorrect

```bash
aws iam --list-groups
```

### Correct

```bash
aws iam list-groups
```

The operation comes immediately after the service name.

---

### Incorrect

```bash
file//01-AWS/JSON/ec2-trust-policy.json
```

### Correct

```bash
file://01-AWS/JSON/ec2-trust-policy.json
```

The `file://` URI prefix is required when passing a local JSON file to AWS CLI parameters that accept file input.

---

# 13. Useful Verification Workflow

For troubleshooting an EC2 IAM Role, the following sequence is useful:

```bash
aws sts get-caller-identity
```

```bash
aws configure list
```

```bash
curl -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

```bash
curl -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/iam/security-credentials/ChurchEC2S3Role
```

Then test the specific AWS operation:

```bash
aws s3 ls s3://skyshield-abiodun-2026
```

This helps establish:

1. **Who am I?** → STS
2. **Where are my credentials coming from?** → `aws configure list`
3. **What role is attached?** → IMDS
4. **Are temporary credentials available?** → IMDS credentials endpoint
5. **What can I actually do?** → Test the required AWS API operation

---

# 14. Security Reminder

Never commit the following to Git:

```text
AWS Access Key IDs
AWS Secret Access Keys
AWS Session Tokens
Private SSH Keys
Passwords
```

Use `.gitignore` and secure credential storage for sensitive information.

For EC2 workloads, prefer:

```text
IAM Role → Instance Profile → Temporary Credentials
```

over:

```text
IAM User → Long-lived Access Keys → EC2
```
