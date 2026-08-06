# IAM Group Policy Lab

## Objective

The objective of this lab was to implement a production-style IAM permission model using the Principle of Least Privilege. The lab demonstrates how to create a customer-managed IAM policy, attach it to an IAM group, assign users to the group, and validate that permissions are enforced correctly.

---

## Architecture

```
                 MediaTeamS3Access Policy
                           │
                           ▼
                    Media-Team Group
                           │
                           ▼
                    media-volunteer
                           │
                           ▼
             Amazon S3 Bucket (skyshield-abiodun-2026)
```

---

## Resources Created

### S3 Bucket

- Bucket Name: `skyshield-abiodun-2026`

### IAM Group

- Media-Team

### IAM User

- media-volunteer

### Customer Managed Policy

- MediaTeamS3Access

---

## Policy Permissions

Allowed:

- s3:ListBucket
- s3:GetObject
- s3:PutObject

Denied (Implicitly):

- s3:DeleteObject
- s3:DeleteBucket
- Any other S3 actions not explicitly allowed

---

## Implementation Steps

1. Created a customer-managed IAM policy.
2. Created the Media-Team IAM group.
3. Attached the policy to the Media-Team group.
4. Created a test IAM user (media-volunteer).
5. Added the user to the Media-Team group.
6. Created programmatic access keys.
7. Configured a dedicated AWS CLI profile.
8. Tested the assigned permissions.

---

## Permission Validation

| Test | Expected | Result |
|-------|----------|--------|
| Verify identity | Pass | ✅ |
| List bucket objects | Allow | ✅ |
| Upload object | Allow | ✅ |
| Download object | Allow | ✅ |
| Delete object | Deny | ✅ |

---

## Observations

### Listing Buckets vs Listing Objects

Running:

aws s3 ls

requires:

s3:ListAllMyBuckets

which was intentionally not granted.

Running:

aws s3 ls s3://skyshield-abiodun-2026

requires only:

s3:ListBucket

which was granted.

---

### IAM Evaluation

AWS evaluates permissions using the following order:

1. Authenticate the identity.
2. Collect all applicable policies.
3. Check for explicit Deny.
4. Check for explicit Allow.
5. If no Allow exists, return an Implicit Deny.

---

## Security Principles Demonstrated

- Principle of Least Privilege
- Group-based Permission Management
- Customer Managed Policies
- Separation of Administrative and Operational Accounts
- Permission Validation
- Defense in Depth
- Implicit Deny
- Resource Scoping

---

## Lessons Learned

- Policies should be attached to groups rather than individual users whenever possible.
- IAM users inherit permissions from group membership.
- AWS denies every request by default unless an Allow exists.
- Explicit Deny always overrides Allow.
- Policy testing should always be performed using a non-administrative identity.
- AWS CLI error messages often reveal the exact missing permission.