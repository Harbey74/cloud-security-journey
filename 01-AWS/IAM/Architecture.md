# AWS IAM Architecture

## 1. Overview

AWS Identity and Access Management (IAM) is the AWS service used to control authentication and authorization within an AWS account.

IAM allows organizations to define:

* Who or what can access AWS resources
* What actions they are allowed to perform
* Which resources they can access
* Under what conditions access is permitted

IAM architecture is primarily built around **identities, policies, roles, trust relationships, and permissions**.

---

## 2. Core IAM Components

The major components covered in this project are:

```text
                        AWS IAM
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
       Users             Groups             Roles
        │                  │                  │
        │                  │                  ├── Trust Policy
        │                  │                  │
        │                  └── Policies       └── Permission Policies
        │
        └── Policies
```

These components serve different purposes and should not be treated as interchangeable.

---

## 3. IAM Users

An IAM User represents an individual identity within an AWS account.

A user can have:

* Console credentials
* Access keys
* Directly attached permissions
* Group-based permissions
* MFA configuration

In the project, an IAM User was created for the media team lab.

```text
IAM User
   │
   ├── Direct Policies
   │
   └── Group Membership
           │
           ▼
       Group Policies
```

Users are generally appropriate for human identities that require long-term access to AWS.

However, long-lived access keys should be avoided where temporary credentials or IAM Roles can be used instead.

---

## 4. IAM Groups

IAM Groups provide a way to organize IAM Users and centrally manage permissions.

Instead of attaching the same policy individually to many users:

```text
User A ── Policy
User B ── Policy
User C ── Policy
User D ── Policy
```

a group can be used:

```text
User A ─┐
User B ─┤
User C ─┼── Media-Team Group ── IAM Policy
User D ─┘
```

Users who belong to the group inherit the permissions provided by the policies attached to the group.

This improves:

* Centralized management
* Consistency
* Scalability
* Operational efficiency
* Least-privilege management

---

## 5. IAM Policies

IAM Policies are JSON documents that define permissions.

A typical identity-based policy contains:

```text
Policy
 │
 ├── Version
 │
 └── Statement
       │
       ├── Sid
       ├── Effect
       ├── Action
       └── Resource
```

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

The policy determines what an identity can do, but it does not determine who can assume an IAM Role.

That responsibility belongs to the Role's trust policy.

---

## 6. IAM Roles

IAM Roles provide identities that can be assumed by trusted entities.

Unlike IAM Users, roles are designed to provide **temporary credentials** to the entity assuming the role.

Roles are commonly used by:

* EC2
* Lambda
* ECS
* Other AWS services
* Applications
* Federated identities
* Cross-account access

The basic architecture is:

```text
Trusted Entity
      │
      ▼
IAM Role
      │
      ├── Trust Policy
      │
      └── Permission Policies
```

---

## 7. Trust Policy

A Role's trust policy defines **who or what is allowed to assume the role**.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

The important elements are:

```text
Principal
    ↓
Who can assume the role?

Action
    ↓
What can they do?
    ↓
sts:AssumeRole
```

The trust policy does **not** grant S3 permissions.

It only establishes the trust relationship necessary for the entity to assume the role.

---

## 8. Permission Policy

After an entity successfully assumes a role, the role's permission policies determine what it can do.

For example:

```text
EC2
 │
 │ AssumeRole
 ▼
ChurchEC2S3Role
 │
 └── Permission Policy
       │
       ├── s3:ListBucket
       ├── s3:GetObject
       └── s3:PutObject
```

If the role does not have an applicable permission, the request is denied through implicit deny.

Therefore:

```text
Trust Policy ≠ Permission Policy
```

Both are required for successful workload access.

---

## 9. IAM Role + EC2 Architecture

EC2 instances can use IAM Roles through an **Instance Profile**.

The architecture is:

```text
                    AWS IAM
                       │
                       ▼
                ChurchEC2S3Role
                 │           │
                 │           │
          Trust Policy   Permission Policy
                 │           │
                 ▼           ▼
               EC2       S3 Permissions
                 ▲
                 │
          Instance Profile
                 │
                 ▼
             EC2 Instance
```

The Instance Profile provides the mechanism for associating the IAM Role with the EC2 instance.

---

## 10. EC2 Instance Profile

An Instance Profile is a container used by EC2 to pass an IAM Role to an instance.

The relationship is:

```text
IAM Role
    │
    ▼
Instance Profile
    │
    ▼
EC2 Instance
```

This distinction is important when managing IAM Roles through the AWS CLI.

The EC2 console works with the Instance Profile associated with the role.

---

## 11. Temporary Credentials and STS

When an EC2 instance assumes an IAM Role, AWS Security Token Service (STS) provides temporary security credentials.

The architecture is:

```text
EC2 Instance
      │
      ▼
Instance Profile
      │
      ▼
IAM Role
      │
      ▼
AWS STS
      │
      ▼
Temporary Credentials
      │
      ├── Access Key ID
      ├── Secret Access Key
      ├── Session Token
      └── Expiration
```

The credentials are temporary and automatically managed by AWS.

This eliminates the need to store long-lived IAM User access keys on the EC2 instance.

---

## 12. EC2 Instance Metadata Service

EC2 workloads can retrieve role credentials through the Instance Metadata Service.

The metadata service is accessible from the instance through:

```text
169.254.169.254
```

The simplified architecture is:

```text
EC2 Application
      │
      ▼
     IMDS
      │
      ▼
Instance Role Credentials
      │
      ▼
Temporary AWS Credentials
```

With IMDSv2, the workload first obtains a metadata token before requesting metadata.

```text
Application
    │
    ▼
Request IMDSv2 Token
    │
    ▼
Metadata Service
    │
    ▼
Temporary Role Credentials
```

---

## 13. Complete EC2 IAM Architecture

The complete architecture demonstrated in the lab is:

```text
                         AWS IAM
                            │
                            ▼
                    ChurchEC2S3Role
                     /             \
                    /               \
                   ▼                 ▼
           Trust Policy       Permission Policy
                │                    │
                │                    ├── s3:ListBucket
                │                    ├── s3:GetObject
                │                    └── s3:PutObject
                │
                ▼
              EC2
                │
                ▼
        Instance Profile
                │
                ▼
        EC2 Instance
                │
                ▼
              IMDSv2
                │
                ▼
       Temporary Credentials
                │
                ▼
              AWS STS
                │
                ▼
          AWS API Request
                │
                ▼
              Amazon S3
```

This architecture separates:

1. **Identity** — IAM Role
2. **Trust** — Trust Policy
3. **Permissions** — Permission Policy
4. **Workload association** — Instance Profile
5. **Credential issuance** — STS
6. **Credential delivery** — IMDSv2
7. **Resource access** — S3

---

## 14. IAM Policy Evaluation

When an AWS API request is made, AWS evaluates the applicable policies.

A simplified model is:

```text
AWS API Request
       │
       ▼
Is there an Explicit Deny?
       │
   ┌───┴───┐
  YES      NO
   │        │
   ▼        ▼
 DENY    Is there an
         applicable Allow?
             │
         ┌───┴───┐
        YES      NO
         │        │
         ▼        ▼
       ALLOW   IMPLICIT DENY
```

An explicit Deny takes precedence over an Allow.

If no applicable Allow exists, the request is denied by default.

---

## 15. IAM User vs IAM Role

### IAM User

```text
Human User
    │
    ▼
IAM User
    │
    ▼
Long-term Credentials
    │
    ▼
AWS Resources
```

### IAM Role

```text
AWS Workload
    │
    ▼
IAM Role
    │
    ▼
STS
    │
    ▼
Temporary Credentials
    │
    ▼
AWS Resources
```

IAM Roles are generally preferred for AWS workloads because they reduce the need to distribute and manage long-lived credentials.

---

## 16. Security Architecture Principles

The IAM architecture implemented in this project follows several security principles:

### Least Privilege

Only the permissions required for the workload should be granted.

For example, the EC2 workload was granted:

```text
s3:ListBucket
s3:GetObject
s3:PutObject
```

but not:

```text
s3:DeleteObject
```

because deletion was not required.

### Centralized Permission Management

IAM Groups allow permissions to be managed centrally for human users.

IAM Roles provide centralized permission management for workloads.

### Temporary Credentials

Workloads should use temporary credentials instead of storing long-lived access keys wherever possible.

### Reduced Blast Radius

Restricting permissions to the required actions and resources reduces the potential impact of a compromised identity or workload.

---

## 17. Architecture Summary

The IAM architecture can be summarized as:

```text
                    IDENTITIES
                       │
          ┌────────────┴────────────┐
          │                         │
        Users                     Roles
          │                         │
       Groups                Trust Policies
          │                         │
       Policies              Permission Policies
          │                         │
          └────────────┬────────────┘
                       │
                       ▼
                 AWS Resources
```

For AWS workloads:

```text
Workload
   ↓
IAM Role
   ↓
Trust Relationship
   ↓
Instance Profile / Service Integration
   ↓
STS Temporary Credentials
   ↓
AWS API
   ↓
Permission Evaluation
   ↓
AWS Resource
```

The key architectural principle is to separate **who the identity is**, **who is trusted to assume it**, and **what that identity is authorized to do**.
