# Production S3 Deployment Lab

## Objective

The objective of this lab was to deploy and secure an Amazon S3 bucket using AWS security best practices.

The lab focused on implementing and validating security and governance controls including Block Public Access, server-side encryption, versioning, resource tagging, and lifecycle management.

## Date

03 August 2026

## Architecture

```text
                    S3 Bucket
             skyshield-abiodun-2026
                        │
       ┌────────────────┼────────────────┐
       │                │                │
       ▼                ▼                ▼
 Block Public       SSE-S3           Versioning
    Access         Encryption         Enabled
       │                │                │
       └────────────────┼────────────────┘
                        │
                        ▼
                Lifecycle Policy
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
          After 90 Days       After 365 Days
              │                   │
              ▼                   ▼
        S3 Standard-IA     Glacier Flexible
                            Retrieval
                                  │
                                  ▼
                         Retained Indefinitely
```

## Security Controls Implemented

### 1. Block Public Access

S3 Block Public Access was enabled to reduce the risk of accidental public exposure of bucket data.

The configuration was verified using:

```bash
aws s3api get-public-access-block --bucket skyshield-abiodun-2026 --profile skyshield
```

### 2. Server-Side Encryption

S3 server-side encryption using SSE-S3 was configured and verified.

The encryption configuration was checked using:

```bash
aws s3api get-bucket-encryption --bucket skyshield-abiodun-2026 --profile skyshield
```

### 3. Versioning

Bucket versioning was enabled to provide protection against accidental overwriting or deletion of objects.

Versioning was enabled using:

```bash
aws s3api put-bucket-versioning --bucket skyshield-abiodun-2026 --versioning-configuration Status=Enabled --profile skyshield
```

The configuration was subsequently verified using:

```bash
aws s3api get-bucket-versioning --bucket skyshield-abiodun-2026 --profile skyshield
```

### 4. Resource Tagging

Tags were applied to the bucket to support resource identification, ownership, governance, and future cost management.

The following tags were applied:

```text
Project     = Skyshield
Environment = Lab
Owner       = Abiodun
Purpose     = CloudSecurityLearning
```

Tags were applied using:

```bash
aws s3api put-bucket-tagging --bucket skyshield-abiodun-2026 --tagging "TagSet=[{Key=Project,Value=Skyshield},{Key=Environment,Value=Lab},{Key=Owner,Value=Abiodun},{Key=Purpose,Value=CloudSecurityLearning}]" --profile skyshield
```

The tags were verified using:

```bash
aws s3api get-bucket-tagging --bucket skyshield-abiodun-2026 --profile skyshield
```

### 5. Lifecycle Management

A lifecycle policy was configured to transition objects to lower-cost storage classes as they age.

The lifecycle strategy was:

```text
Day 0
  │
  ▼
S3 Standard
  │
  │ After 90 days
  ▼
S3 Standard-IA
  │
  │ After 365 days
  ▼
S3 Glacier Flexible Retrieval
  │
  ▼
Retained indefinitely
```

No automatic object deletion was configured.

This approach preserves the data while reducing storage costs as objects become less frequently accessed.

## Implementation

1. Created the `skyshield-abiodun-2026` S3 bucket.
2. Verified the bucket configuration using the AWS CLI.
3. Verified S3 Block Public Access.
4. Verified server-side encryption.
5. Enabled bucket versioning.
6. Verified the versioning configuration.
7. Applied resource tags to the bucket.
8. Verified the applied tags.
9. Configured an S3 lifecycle policy.
10. Configured lifecycle transitions from S3 Standard to S3 Standard-IA after 90 days.
11. Configured transition to Glacier Flexible Retrieval after 365 days.
12. Intentionally excluded automatic deletion to retain the data indefinitely.
13. Re-validated the implemented security controls using the AWS CLI.

## Validation

Every implemented security control was validated after configuration.

| Security Control | Expected | Result |
|---|---|---|
| Block Public Access | Enabled | ✅ |
| SSE-S3 Encryption | Enabled | ✅ |
| Versioning | Enabled | ✅ |
| Resource Tags | Applied | ✅ |
| Lifecycle Policy | Configured | ✅ |
| Automatic Deletion | Not configured | ✅ |

## Observations

### Configuration Should Be Verified

A successful configuration command does not replace verification.

Each important security control was queried after implementation to confirm that the intended configuration was actually applied.

This follows an important operational principle:

```text
Configure → Verify → Document
```

### Versioning and Data Protection

Versioning provides an additional layer of protection against accidental object deletion and overwriting.

It is particularly useful for data that should be retained and recoverable.

### Lifecycle Management and Cost Optimization

Not all data requires the same storage class throughout its entire lifecycle.

The lifecycle policy allows older, less frequently accessed data to transition to cheaper storage classes while remaining available for future retrieval.

### No Automatic Deletion

Automatic deletion was intentionally excluded from the lifecycle policy.

The purpose of this lab was to retain the data while optimizing storage costs rather than permanently deleting objects after a fixed period.

## Security Principles Demonstrated

- Defense in Depth
- Data Protection
- Secure Configuration
- Data Retention
- Resource Governance
- Storage Cost Optimization
- Configuration Validation
- Risk Reduction

## Lessons Learned

1. Security controls should be deliberately configured and verified.
2. Block Public Access reduces the risk of unintended public exposure.
3. Encryption protects data at rest.
4. Versioning provides protection against accidental modification or deletion.
5. Resource tagging improves governance and resource identification.
6. Lifecycle policies can reduce long-term storage costs.
7. Storage classes should be selected based on access requirements.
8. Retention requirements should be considered before configuring automatic deletion.
9. AWS CLI provides useful commands for both configuration and validation.
10. Security configuration should never be assumed to be correct without verification.

## Final Security Baseline

The S3 bucket was configured with the following baseline:

- Block Public Access enabled
- SSE-S3 encryption enabled
- Versioning enabled
- Resource tagging applied
- Lifecycle policy configured
- Objects transition to Standard-IA after 90 days
- Objects transition to Glacier Flexible Retrieval after 365 days
- No automatic deletion configured
- All implemented controls validated using the AWS CLI

## Lab Outcome

The lab successfully demonstrated how to establish a secure and manageable S3 bucket baseline using AWS security and governance controls.

The resulting configuration provides protection against accidental public exposure, supports data recovery through versioning, protects data at rest through encryption, improves resource governance through tagging, and optimizes long-term storage costs through lifecycle management.