# IAM Security Concepts

## What is IAM?

Identity and Access Management (IAM) is AWS's authentication and authorization service used to securely control access to AWS resources.

IAM answers two questions:

- Who are you?
- What are you allowed to do?

---

# Authentication vs Authorization

Authentication

Verifies the identity making the request.

Example:

"I am media-volunteer."

Authorization

Determines what the authenticated identity is permitted to do.

Example:

"media-volunteer may upload objects but cannot delete them."

---

# IAM Components

## IAM Users

Represents an individual identity.

Example:

- abiodun-admin
- media-volunteer

---

## IAM Groups

Logical collection of IAM users.

Purpose:

Simplifies permission management by assigning permissions to the group instead of individual users.

---

## IAM Roles

Temporary identities assumed by AWS services, applications, or users.

Preferred over long-lived access keys for AWS services.

Examples:

- EC2
- Lambda
- ECS
- CloudFormation

---

## IAM Policies

JSON documents defining permissions.

Policies answer:

- Which actions?
- On which resources?
- Under what effect?

---

# IAM Policy Structure

Version

Specifies the policy language version.

Current standard:

2012-10-17

---

Statement

Contains one or more permission rules.

---

Sid

Statement Identifier.

Optional.

Improves readability.

---

Effect

Either:

Allow

or

Deny

---

Action

Defines the operation.

Examples:

- s3:GetObject
- s3:PutObject
- s3:DeleteObject

---

Resource

Specifies where the permission applies.

Bucket:

arn:aws:s3:::bucket-name

Objects:

arn:aws:s3:::bucket-name/*

---

# Least Privilege

Grant only the permissions required to perform the intended task.

Avoid:

s3:*

Prefer:

- s3:GetObject
- s3:PutObject
- s3:ListBucket

---

# Policy Evaluation Logic

AWS evaluates requests using this sequence:

1. Authenticate identity.
2. Collect all applicable policies.
3. Check Explicit Deny.
4. Check Explicit Allow.
5. Apply Implicit Deny if no Allow exists.

---

# Explicit Deny

Always overrides every Allow.

---

# Implicit Deny

Every action is denied unless explicitly allowed.

---

# Identity-based Policies

Attached to:

- Users
- Groups
- Roles

Controls what identities can do.

---

# Resource-based Policies

Attached directly to AWS resources.

Example:

S3 Bucket Policies

Controls who may access the resource.

---

# Customer Managed Policies

Created and maintained by customers.

Advantages:

- Reusable
- Version controlled
- Easier maintenance
- Can be attached to multiple groups

---

# AWS Managed Policies

Created and maintained by AWS.

Suitable for common use cases.

Usually broader than production least-privilege requirements.

---

# Permission Inheritance

Policy

↓

Group

↓

User

Users inherit permissions from their groups.

---

# Production Best Practices

- Never use root credentials.
- Prefer IAM Roles over long-lived access keys.
- Assign permissions to groups rather than users.
- Follow Least Privilege.
- Validate permissions using test accounts.
- Regularly review unused permissions.
- Scope permissions to specific resources.
- Use customer-managed policies for reusable permission sets.

---

# Key Lessons

- Groups simplify administration.
- Policies define permissions.
- Users inherit permissions.
- AWS starts with Implicit Deny.
- Explicit Deny overrides Allow.
- Resource ARNs determine where permissions apply.
- Least Privilege reduces attack surface.


## Permission Policy vs Trust Policy

                IAM Role
           +-----------------+
           |                 |
           | Permission      |
           | Policy          |
           |                 |
           | "What can       |
           | I do?"          |
           +-----------------+

                    ▲

           +-----------------+
           |                 |
           | Trust Policy    |
           |                 |
           | "Who can        |
           | assume me?"     |
           +-----------------+


A principal must first be trusted to assume the role, and once the role has been assumed, the role must contain the permissions required to perform the requested action on the target resource.


EC2 instances attach to an Instance Profile, not directly to an IAM Role.

IAM Roles reduce secret management risk because temporary credentials are issued dynamically by AWS STS to trusted workloads, eliminating the need to store long-lived access keys on compute instances or in application code.




## IAM Roles, Trust Policies, Temporary Credentials and Workload Identity

### IAM Roles

IAM Roles provide temporary, assumable identities that AWS services, applications, and other trusted entities can use to access AWS resources.

Unlike IAM Users, roles are not intended to have permanent credentials such as long-lived access keys. A role becomes useful when a trusted entity assumes it and receives temporary security credentials through AWS Security Token Service (STS).

Roles are particularly important for AWS workloads such as EC2 instances because they eliminate the need to store long-lived access keys on the instance.

### Trust Policy vs Permission Policy

An IAM Role has two distinct security components:

* **Trust Policy** — determines **who or what is allowed to assume the role**.
* **Permission Policy** — determines **what the role is allowed to do after it has been assumed**.

This creates two separate stages in the authorization process.

For example:

```text
EC2 requests to assume role
        ↓
Trust Policy evaluation
        ↓
Is EC2 trusted?
        ↓
Yes
        ↓
STS issues temporary credentials
        ↓
Application makes AWS API request
        ↓
Permission Policy evaluation
        ↓
Is the requested action allowed?
        ↓
Allow / Deny
```

A trusted principal does not automatically receive permissions to access AWS resources. The role must also have the required permission policies attached.

### Trust Policy Structure

A typical EC2 trust policy contains:

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

Key elements:

* **Version** — identifies the IAM policy language version.
* **Statement** — contains the policy rules.
* **Effect** — specifies whether the statement allows or denies the action. In the role trust policy used in the lab, this was `Allow`.
* **Principal** — identifies who or what is trusted to assume the role. This can be an AWS service, AWS account, federated identity, or other supported principal.
* **Action** — specifies the operation being permitted. For role assumption, this is typically `sts:AssumeRole`.

The `Principal` in a trust policy is fundamentally different from the `Resource` element used in a permission policy. The trust policy answers **who can assume the role**, while the permission policy answers **what the assumed identity can access**.

### Authentication vs Authorization

The IAM Role lab reinforced the distinction between authentication and authorization.

**Authentication** answers:

> Who are you, or what identity are you operating as?

**Authorization** answers:

> What are you allowed to do?

For an EC2 workload:

```text
Trust Policy
    ↓
Allows EC2 to assume the role
    ↓
STS provides temporary credentials
    ↓
Authentication established
    ↓
Permission Policy evaluates requested action
    ↓
Authorization decision
```

A workload can successfully assume a role but still be denied access to an AWS resource if the role lacks the required permission.

### Explicit Allow, Explicit Deny and Implicit Deny

AWS IAM policy evaluation follows an important principle:

* An **explicit Deny** overrides an Allow.
* An **explicit Allow** grants the requested permission when applicable.
* If no applicable Allow exists, the request is denied by the default **implicit deny**.

For example, if an EC2 role can assume successfully but has no policy allowing `s3:ListBucket`, the request is denied even though there is no explicit Deny.

This distinction was demonstrated during the EC2-to-S3 lab.

### Instance Profiles

EC2 instances do not attach an IAM Role directly. Instead, an **IAM Instance Profile** acts as the mechanism through which an EC2 instance receives an IAM Role.

The relationship can be represented as:

```text
IAM Role
   ↓
Instance Profile
   ↓
EC2 Instance
```

When a role is created through the AWS CLI, the Instance Profile may need to be created separately and the role added to it.

Commands used during the lab included:

```bash
aws iam create-instance-profile
```

and:

```bash
aws iam add-role-to-instance-profile
```

This distinction is important because the EC2 console expects an Instance Profile rather than the raw IAM Role.

### AWS STS Temporary Credentials

AWS Security Token Service (STS) provides temporary security credentials when an identity assumes an IAM Role.

Temporary credentials contain:

* Access Key ID
* Secret Access Key
* Session Token
* Expiration time

These credentials are different from IAM User access keys because they are time-limited and automatically managed by AWS.

The EC2 workload therefore does not need permanent AWS credentials stored in:

```text
~/.aws/credentials
```

This significantly reduces the risk associated with credential leakage and long-lived secrets.

### EC2 Instance Metadata Service (IMDS)

EC2 instances can obtain information and temporary role credentials through the Instance Metadata Service (IMDS).

The metadata service is available from within the EC2 instance through the link-local address:

```text
169.254.169.254
```

During the lab, IMDS was queried using IMDSv2 and returned the credentials associated with:

```text
ChurchEC2S3Role
```

The response included an expiration timestamp, demonstrating that the credentials are temporary.

### IMDSv2

IMDSv2 introduces a session-oriented mechanism for accessing instance metadata.

The workflow used during the lab was:

```text
Request IMDSv2 token
        ↓
Use token in metadata request
        ↓
Retrieve role information
        ↓
Retrieve temporary credentials
```

IMDSv2 is an important security control because it helps reduce the risk associated with certain SSRF-based attacks against the instance metadata service.

### IAM Roles and Least Privilege

IAM Roles should follow the Principle of Least Privilege.

During the lab, the EC2 role was intentionally granted only:

```text
s3:ListBucket
s3:GetObject
s3:PutObject
```

for the specific S3 bucket:

```text
arn:aws:s3:::skyshield-abiodun-2026
```

and its objects:

```text
arn:aws:s3:::skyshield-abiodun-2026/*
```

`s3:DeleteObject` was deliberately excluded because deletion was not required for the workload.

This limits the blast radius if the EC2 instance, application, or credentials are compromised.

### IAM Roles vs Long-Lived Access Keys

For AWS workloads, IAM Roles are preferable to embedding IAM User access keys in applications or EC2 instances.

**Long-lived access key approach:**

```text
Application
    ↓
Access Key + Secret Key
    ↓
AWS
```

Risks include:

* Accidental exposure
* Credentials committed to source control
* Credential theft
* Difficult rotation
* Larger operational overhead
* Increased blast radius if compromised

**IAM Role approach:**

```text
EC2
 ↓
Instance Profile
 ↓
IAM Role
 ↓
STS
 ↓
Temporary Credentials
 ↓
AWS API
```

This approach reduces secret-management overhead and allows permissions to be centrally managed through the role.

### Defense in Depth

The EC2 IAM Role implementation demonstrated multiple security controls working together:

```text
Trusted Principal
        +
IAM Role
        +
Least-Privilege Permission Policy
        +
Resource-Level Restrictions
        +
Temporary Credentials
        +
IMDSv2
        =
Reduced Attack Surface and Blast Radius
```

No single control provides complete security. Combining these controls creates a stronger security architecture.

### Key Takeaways

* IAM Roles provide identities for AWS workloads without requiring long-lived credentials.
* Trust policies determine **who can assume a role**.
* Permission policies determine **what the assumed role can do**.
* Successfully assuming a role does not automatically grant resource access.
* EC2 uses an Instance Profile to associate an IAM Role with an instance.
* STS provides temporary credentials for assumed-role sessions.
* IMDS provides role credentials to workloads running on EC2.
* IMDSv2 adds an important security layer around metadata access.
* Least privilege should be applied to both actions and resources.
* Explicit Deny overrides Allow, while the absence of an applicable Allow results in implicit Deny.
* IAM Roles significantly reduce the need to manage long-lived credentials on AWS workloads.
                |

