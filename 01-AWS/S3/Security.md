# Amazon S3 Security

## Block Public Access

Purpose:
Prevent accidental public exposure of S3 buckets and objects.

Components:
- BlockPublicAcls
- IgnorePublicAcls
- BlockPublicPolicy
- RestrictPublicBuckets

Recommendation:
Always enable Block Public Access unless there is a documented business requirement not to.


## IAM Policies vs Bucket Policies

IAM Policy
- Identity-based
- Attached to users, groups, or roles
- Defines what actions an identity can perform

Bucket Policy
- Resource-based
- Attached directly to an S3 bucket
- Defines who or what can access the bucket

Important:
AWS evaluates both identity-based and resource-based policies when making an authorization decision.


## What's the difference between an IAM policy and a bucket policy?

IAM policies are attached to identities such as users, groups, or roles and define what actions those identities can perform. Bucket policies are resource-based policies attached directly to an S3 bucket that define which principals can access that specific bucket and under what conditions. AWS evaluates both when determining whether access should be granted.

An explicit Deny always wins.

Identity-based policies → "What can this user/role do?"
Resource-based policies → "Who can access this resource?"

## What are this service's security controls, and what risk does each one mitigate?

| Security Control    | Risk Mitigated                           |
| ------------------- | ---------------------------------------- |
| IAM Policies        | Unauthorized identity actions            |
| Bucket Policies     | Unauthorized access to a specific bucket |
| Block Public Access | Accidental public exposure               |


## Amazon S3 Security
Server-Side Encryption (SSE)
Purpose

Protects data at rest by encrypting objects stored in Amazon S3.

## Types of Server-Side Encryption
SSE-S3 (Amazon S3 Managed Keys)
-- AWS manages the encryption keys.
-- Simplest option to implement.
-- Suitable for general-purpose workloads.
-- Minimal operational overhead.
SSE-KMS (AWS Key Management Service)
-- Uses AWS KMS to manage encryption keys.
-- Provides fine-grained access control.
-- Supports auditing through CloudTrail.
-- Recommended for highly sensitive or regulated data.
SSE-C (Customer-Provided Keys)
-- Customer supplies and manages encryption keys.
-- AWS never stores the keys.
-- Highest level of control but also the highest operational complexity.

## Encryption in Transit vs Encryption at Rest
Encryption in Transit

Protects data while moving across the network using HTTPS/TLS.

Encryption at Rest

Protects stored objects using server-side encryption.

Why Encrypt Data?

Encryption protects the confidentiality of data.

It ensures that:

-- Unauthorized users cannot read stored data.
-- Physical compromise of storage media does not expose customer information.
-- Multiple layers of security (Defense in Depth) protect organizational assets.


## Encryption Keys
| Encryption Type | Managed By    | Typical Use                                |
| --------------- | ------------- | ------------------------------------------ |
| SSE-S3          | AWS           | General-purpose workloads                  |
| SSE-KMS         | AWS KMS / You | Production systems, regulated environments |
| SSE-C           | Customer      | Specialized compliance requirements        |


## Storage System Comparism


| Storage Type                   | Modification Style                         |
| ------------------------------ | ------------------------------------------ |
| Local File System (NTFS, ext4) | Files are modified in place                |
| Database                       | Rows are updated                           |
| Git                            | Stores history and differences efficiently |
| **Amazon S3**                  | Stores a new immutable object              |


## Objects in S3 are immutable.

That means once an object is written, AWS doesn't modify it in place.

When you "overwrite" an object:

AWS doesn't actually edit the existing object.

Instead, it stores a new object with a new Version ID and makes that the current version.

The previous version remains untouched.

This immutability is one of the reasons S3 is so durable and reliable.

## Optimize for durability first, then optimize for cost.

## Only retain data for as long as there's a legitimate business or legal need

## Data Governance

| Feature             | Primary Purpose                                       |
| ------------------- | ----------------------------------------------------- |
| IAM Policies        | Identity authorization                                |
| Bucket Policies     | Resource authorization                                |
| Block Public Access | Prevent accidental public exposure                    |
| Encryption          | Protect confidentiality                               |
| Versioning          | Recover from accidental or malicious changes          |
| Lifecycle Policies  | Manage retention, storage cost, and automated cleanup |


## Production Security Baseline for S3 Buckets

✓ Block Public Access

✓ Default Encryption

✓ Versioning

✓ Lifecycle Policies

✓ Logging

✓ Least-Privilege IAM

✓ MFA for privileged users

✓ Bucket naming standard

✓ Resource tags

✓ Periodic access reviews

## Data Classification

| Data Type                     | Suggested Encryption                                   |
| ----------------------------- | ------------------------------------------------------ |
| Public assets                 | SSE-S3                                                 |
| Internal documents            | SSE-S3                                                 |
| Customer personal information | SSE-S3 or SSE-KMS (depending on organizational policy) |
| Financial records             | SSE-KMS                                                |
| Highly regulated data         | SSE-KMS with customer-managed keys                     |

## Defense in Depth

Amazon S3 security uses multiple layers rather than relying on a single control.

| Security Control    | Purpose                                      |
| ------------------- | -------------------------------------------- |
| IAM Policies        | Identity-based authorization                 |
| Bucket Policies     | Resource-based authorization                 |
| Block Public Access | Prevent accidental public exposure           |
| Encryption          | Protect confidentiality                      |
| Versioning          | Recover from accidental or malicious changes |
| Lifecycle Policies  | Manage retention and storage cost            |


## Amazon S3 Versioning
Purpose

Versioning preserves previous versions of an object.

It protects against:

-- Accidental deletion
-- Accidental overwrite
-- Some ransomware scenarios
-- Insider mistakes

How Versioning Works

Versioning is based on the object key (name/path), not the file contents.

Example:

reports/report.pdf

Uploading another object with the same key creates a new version, regardless of whether the contents changed.

Changing the object name creates a completely new object instead of another version.

Object Immutability

Objects stored in S3 are immutable.

Updating an object does not modify the existing object.

Instead:

AWS stores a completely new object.
A new Version ID is assigned.
Previous versions remain unchanged.
Delete Marker

When Versioning is enabled, deleting an object does not immediately remove the stored data.

Instead AWS creates a Delete Marker.



## Amazon S3 Lifecycle Policies
Purpose

Lifecycle Policies automatically manage objects as they age.

Benefits include:

Cost optimization
Data retention management
Compliance
Automated cleanup
Common Lifecycle Actions
Transition Objects

Move objects to cheaper storage classes.

Example:

S3 Standard
S3 Standard-IA
Glacier Instant Retrieval
Glacier Flexible Retrieval
Glacier Deep Archive
Delete Current Objects

Automatically remove objects after a specified retention period.

Delete Non-Current Versions

Automatically remove previous versions created by Versioning.

Remove Expired Delete Markers

Automatically remove Delete Markers when configured.

Why Lifecycle Policies Matter

Without Lifecycle Policies:

Storage costs continue to grow.
Old versions remain indefinitely.
Delete Markers persist forever.
Data retention becomes inconsistent.

Lifecycle Policies automate storage management according to organizational requirements.

# Key Takeaways

- Amazon S3 objects are immutable.
- Versioning is based on the object key, not file contents.
- Every object version consumes storage.
- Delete operations create Delete Markers instead of immediately removing data when Versioning is enabled.
- Lifecycle Policies automate storage management but never act unless explicitly configured.
- Encryption protects confidentiality.
- Versioning provides recovery.
- Lifecycle Policies support governance and cost optimization.
- Security decisions should be based on business requirements and data classification.
- Always think in terms of Defense in Depth rather than relying on a single security control.
- Objects smaller than 128 KB won't automatically transition to another storage class by default in lifecycle policy