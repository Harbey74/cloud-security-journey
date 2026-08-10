# Amazon S3 Architecture

## 1. Overview

Amazon Simple Storage Service (Amazon S3) is an object storage service designed to store and retrieve data at scale.

S3 uses a simple object-storage architecture built around:

* Buckets
* Objects
* Object keys
* Prefixes
* Regions
* Storage classes
* Access controls
* Lifecycle configurations

The fundamental S3 architecture can be represented as:

```text
                         Amazon S3
                            │
                            ▼
                         Bucket
                            │
             ┌──────────────┼──────────────┐
             │              │              │
          Object          Object          Object
             │              │              │
           Key            Key            Key
             │              │              │
          Data +        Data +        Data +
         Metadata      Metadata      Metadata
```

---

## 2. S3 Buckets

A bucket is the top-level container used to store objects in Amazon S3.

For example:

```text
Bucket:
skyshield-abiodun-2026
```

A bucket provides the namespace under which objects are stored.

The architecture is:

```text
AWS Account
    │
    ▼
S3 Bucket
    │
    ├── Object
    ├── Object
    ├── Object
    └── Object
```

A bucket name must be globally unique within the S3 namespace.

---

## 3. S3 Objects

An S3 object is the actual unit of data stored in a bucket.

An object consists primarily of:

* Object data
* Object key
* Metadata

Conceptually:

```text
Object
 ├── Key
 ├── Data
 └── Metadata
```

For example:

```text
Bucket:
skyshield-abiodun-2026

Object:
lab1.txt
```

The object is identified using its key.

---

## 4. Object Keys

An S3 object key uniquely identifies an object within a bucket.

For example:

```text
lab1.txt
```

or:

```text
recordings/2026/08/service.mp4
```

S3 does not use a traditional filesystem hierarchy.

Instead, what appears to be a folder structure is created using prefixes within object keys.

For example:

```text
recordings/2026/08/service.mp4
```

can be interpreted as:

```text
Prefix: recordings/2026/08/
Object: service.mp4
```

---

## 5. S3 Prefixes and Folders

S3 does not have traditional folders in the same way that a local filesystem does.

The following:

```text
recordings/
    2026/
        08/
            service.mp4
```

is logically represented by the object key:

```text
recordings/2026/08/service.mp4
```

Therefore:

```text
S3 Bucket
    │
    ├── recordings/2026/08/service.mp4
    ├── recordings/2026/08/testimony.mp4
    └── recordings/2026/09/service.mp4
```

The AWS console presents these prefixes as folders to make the structure easier for humans to navigate.

---

## 6. S3 Regions

S3 buckets are created in a specific AWS Region.

For this project, the AWS CLI profile is configured to use:

```text
eu-west-1
```

which is the Europe (Ireland) Region.

Conceptually:

```text
AWS
 │
 ├── Region: eu-west-1
 │      │
 │      └── S3 Bucket
 │
 ├── Region: other-region
 │      │
 │      └── Other Resources
 │
 └── ...
```

The Region selected for a bucket affects where the bucket's data is stored and can also affect latency, compliance considerations, and data-transfer costs.

---

## 7. S3 Data Model

The S3 data model can be summarized as:

```text
AWS Account
     │
     ▼
   Bucket
     │
     ├── Object
     │     ├── Key
     │     ├── Data
     │     └── Metadata
     │
     ├── Object
     │     ├── Key
     │     ├── Data
     │     └── Metadata
     │
     └── Object
           ├── Key
           ├── Data
           └── Metadata
```

Unlike a traditional filesystem, S3 does not require directories to store objects.

---

## 8. S3 Access Architecture

Access to S3 resources is controlled through AWS authorization mechanisms.

A simplified architecture is:

```text
Identity
   │
   ▼
IAM
   │
   ├── Identity Policy
   │
   └── Other Applicable Policies
   │
   ▼
S3 API Request
   │
   ▼
S3 Authorization
   │
   ▼
Bucket / Object
```

For example:

```text
IAM User / IAM Role
        │
        │ s3:GetObject
        ▼
S3 Bucket
        │
        ▼
Object
```

The identity must have the appropriate permissions for the requested operation.

---

## 9. Bucket-Level vs Object-Level Permissions

One of the important architectural concepts in S3 is the distinction between **bucket-level operations** and **object-level operations**.

### Bucket-Level Operation

For example:

```text
s3:ListBucket
```

The resource is the bucket itself:

```text
arn:aws:s3:::skyshield-abiodun-2026
```

### Object-Level Operation

For example:

```text
s3:GetObject
s3:PutObject
s3:DeleteObject
```

The resource is an object within the bucket:

```text
arn:aws:s3:::skyshield-abiodun-2026/*
```

The distinction can be represented as:

```text
Bucket
│
└── arn:aws:s3:::skyshield-abiodun-2026
       │
       └── Objects
              │
              ├── lab1.txt
              ├── volunteer-test.txt
              └── ec2-role-test.txt
```

This distinction is important when designing least-privilege S3 policies.

---

## 10. S3 API Operations

Applications and users interact with S3 through API operations.

Common operations include:

```text
ListBucket
    │
    └── List objects in a bucket

GetObject
    │
    └── Download/read an object

PutObject
    │
    └── Upload an object

DeleteObject
    │
    └── Delete an object
```

The architecture is:

```text
Client
  │
  ▼
AWS API
  │
  ▼
S3
  │
  ▼
Bucket
  │
  ▼
Object
```

Each API operation requires the appropriate IAM permission.

---

## 11. S3 Security Architecture

S3 security involves multiple layers.

A simplified model is:

```text
                   S3 Security
                       │
       ┌───────────────┼────────────────┐
       │               │                │
       ▼               ▼                ▼
 IAM Policies    Bucket Policies   Block Public Access
       │               │                │
       └───────────────┼────────────────┘
                       ▼
                 S3 Authorization
                       │
                       ▼
                   Resources
```

These controls work together to determine whether an S3 request should be allowed or denied.

---

## 12. S3 Block Public Access

S3 Block Public Access provides controls designed to prevent unintended public access to S3 resources.

The settings include:

```text
BlockPublicAcls
IgnorePublicAcls
BlockPublicPolicy
RestrictPublicBuckets
```

In the project bucket, Block Public Access was enabled.

The architecture is:

```text
Public Access Request
        │
        ▼
Block Public Access
        │
        ├── Blocked
        │
        └── Continue to authorization
```

Block Public Access does not replace IAM authorization.

An authenticated IAM identity with appropriate permissions can still access the bucket according to the applicable authorization policies.

---

## 13. IAM Policy + S3 Architecture

An IAM identity can receive permissions through an identity-based policy.

Example:

```text
IAM User
    │
    ▼
IAM Group
    │
    ▼
MediaTeamS3Access
    │
    ├── s3:ListBucket
    ├── s3:GetObject
    └── s3:PutObject
    │
    ▼
S3 Bucket
```

The permissions are limited to the required S3 operations.

This follows the Principle of Least Privilege.

---

## 14. EC2 IAM Role + S3 Architecture

The EC2 IAM Role lab demonstrated how an EC2 instance can access S3 without storing long-lived access keys.

The architecture is:

```text
EC2 Instance
      │
      ▼
IAM Instance Profile
      │
      ▼
ChurchEC2S3Role
      │
      ├── Trust Policy
      │       │
      │       └── EC2 allowed to assume role
      │
      └── Permission Policy
              │
              ├── s3:ListBucket
              ├── s3:GetObject
              └── s3:PutObject
                      │
                      ▼
                 S3 Bucket
```

The EC2 instance receives temporary credentials through the role.

This is preferable to storing IAM User access keys on the instance.

---

## 15. S3 Lifecycle Architecture

S3 Lifecycle Management allows objects to transition between storage classes or be deleted automatically based on defined rules.

The architecture is:

```text
                S3 Object
                    │
                    ▼
             Lifecycle Rule
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
     Transition            Expiration
          │                   │
          ▼                   ▼
Different Storage       Object Deletion
Classes
```

For example, frequently accessed data may initially be stored in a standard storage class and later transitioned to a lower-cost storage class.

Lifecycle rules can help optimize storage costs and automate data retention.

---

## 16. S3 Storage Classes

S3 provides different storage classes designed for different access patterns.

Common examples include:

```text
S3 Standard
S3 Intelligent-Tiering
S3 Standard-IA
S3 One Zone-IA
S3 Glacier Instant Retrieval
S3 Glacier Flexible Retrieval
S3 Glacier Deep Archive
```

Conceptually:

```text
Frequent Access
      │
      ▼
S3 Standard
      │
      ▼
Less Frequent Access
      │
      ▼
S3 Standard-IA / Intelligent-Tiering
      │
      ▼
Archive
      │
      ▼
Glacier / Deep Archive
```

The appropriate storage class depends on access frequency, retrieval requirements, durability requirements, and cost objectives.

---

## 17. S3 Lifecycle and Cost Optimization

Lifecycle policies can automatically transition objects as their access frequency decreases.

For example:

```text
Day 0
  │
  ▼
S3 Standard
  │
  │ 30 days
  ▼
Infrequent Access
  │
  │ 90 days
  ▼
Archive Storage
  │
  │ Retention period expires
  ▼
Deletion
```

This is particularly useful for long-term storage such as:

* Church service recordings
* Backups
* Historical logs
* Compliance archives
* Media archives

---

## 18. S3 Durability and Availability

S3 is designed for extremely high durability of stored objects.

Durability refers to the likelihood that data will not be lost.

Availability refers to the expected accessibility of the service.

These concepts are different:

```text
Durability
    │
    └── How reliably data is preserved

Availability
    │
    └── How reliably the service can be accessed
```

Storage class selection can affect availability characteristics and cost.

---

## 19. S3 Data Flow in This Project

The practical S3 workflow implemented in this project can be represented as:

```text
Local Windows PC
       │
       │ AWS CLI
       ▼
AWS CLI Profile
       │
       ▼
AWS IAM
       │
       ▼
Authorization
       │
       ▼
S3
       │
       ▼
skyshield-abiodun-2026
       │
       ├── lab1.txt
       ├── volunteer-test.txt
       └── Other Objects
```

For the EC2 workload:

```text
EC2
 │
 ▼
IAM Role
 │
 ▼
Temporary Credentials
 │
 ▼
AWS CLI
 │
 ▼
S3 Authorization
 │
 ▼
skyshield-abiodun-2026
```

---

## 20. S3 CLI Architecture

The AWS CLI provides a command-line interface for interacting with S3.

Common operations used in this project include:

```text
aws s3 ls
      │
      └── List buckets or objects

aws s3 cp
      │
      └── Upload/download objects

aws s3 rm
      │
      └── Delete objects
```

The general architecture is:

```text
AWS CLI
   │
   ▼
AWS API
   │
   ▼
S3
   │
   ▼
Bucket
   │
   ▼
Object
```

---

## 21. S3 Architecture Summary

The complete S3 architecture can be summarized as:

```text
                         AWS
                          │
                          ▼
                       Region
                          │
                          ▼
                     S3 Bucket
                          │
            ┌─────────────┼─────────────┐
            │             │             │
         Object         Object        Object
            │             │             │
           Key           Key           Key
            │             │             │
            └─────────────┼─────────────┘
                          │
                          ▼
                   S3 Authorization
                          │
             ┌────────────┼────────────┐
             │            │            │
             ▼            ▼            ▼
          IAM Policy   Bucket Policy   Block Public
                                      Access
             │
             └────────────┬───────────┘
                          ▼
                     API Request
                          │
                          ▼
                  S3 Resource Access
```

---

## 22. Key Architectural Principles

The S3 architecture covered in this project demonstrates several important principles:

### 1. Object Storage

S3 stores data as objects rather than traditional filesystem files and directories.

### 2. Bucket-Based Organization

Buckets provide the top-level container for objects.

### 3. Key-Based Object Identification

Objects are identified using unique keys within a bucket.

### 4. Separation of Bucket and Object Permissions

Bucket-level actions such as `ListBucket` and object-level actions such as `GetObject` require appropriately scoped permissions.

### 5. Centralized Authorization

IAM policies and other S3 access controls determine whether requests are authorized.

### 6. Least Privilege

Only the permissions required for a workload or user should be granted.

### 7. Workload Identity

EC2 workloads should use IAM Roles and temporary credentials rather than long-lived access keys.

### 8. Automated Data Management

Lifecycle policies can automate storage-class transitions and object expiration.

### 9. Defense in Depth

S3 security should use multiple controls, including IAM, bucket policies, Block Public Access, encryption, and appropriate data-management controls.

---

## 23. Final S3 Architecture Model

The S3 architecture can ultimately be viewed as:

```text
                         CLIENT
                           │
                           ▼
                   IAM Identity / Role
                           │
                           ▼
                     AWS API Request
                           │
                           ▼
                  ┌──────────────────┐
                  │ S3 Authorization │
                  └──────────────────┘
                           │
             ┌─────────────┼─────────────┐
             │             │             │
             ▼             ▼             ▼
        IAM Policies   Bucket Policy   Block Public
                                      Access Controls
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                       S3 Bucket
                           │
                           ▼
                         Object
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
             Data       Metadata       Key
                           │
                           ▼
                   Lifecycle Management
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
      Storage Transition             Expiration
```

The core S3 architecture is therefore based on the relationship between **buckets, objects, keys, AWS identities, authorization policies, and lifecycle management**.
