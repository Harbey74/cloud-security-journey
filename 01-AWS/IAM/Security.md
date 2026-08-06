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













# Amazon IAM Security

Good identity management must scale as the organization grows.
Explicit Deny always overrides Allow.

Permissions should be based on job function, not on individual people.

## IAM Role
It's an identity that can be assumed by a trusted entity.
Never hard-code AWS credentials. Whenever possible, use an IAM Role.

Why Temporary Credentials Matter

Suppose an attacker somehow obtains temporary credentials.

Unlike long-lived access keys:

They expire automatically.
They can't be used indefinitely.
There's no secret key stored in your application configuration.

That's a significant reduction in risk.

| Identity  | Used For                                                         |
| --------- | ---------------------------------------------------------------- |
| IAM User  | Individual people (mainly for learning and smaller environments) |
| IAM Group | Managing permissions for collections of users                    |
| IAM Role  | AWS services, applications, and temporary access                 |

