# Changelog

All notable changes and milestones in the Project SkyShield – Cloud Security Engineer Journey are documented here.

---

## [2026-08-10] – IAM Roles, Temporary Credentials & EC2 Integration

### Added

* IAM Roles and Temporary Credentials learning module.
* IAM trust policy documentation.
* IAM permission policy documentation.
* EC2 IAM Role lab.
* IAM architecture documentation.
* IAM security documentation.
* IAM command reference.
* EC2-to-S3 role-based access testing.

### Security Concepts

* Learned the difference between trust policies and permission policies.
* Learned how AWS STS provides temporary credentials when an IAM role is assumed.
* Learned how EC2 uses IAM Instance Profiles to obtain role credentials.
* Explored the EC2 Instance Metadata Service (IMDS) as a source of temporary credentials.
* Applied the Principle of Least Privilege to EC2 workloads.
* Compared IAM roles with long-lived access keys.
* Tested allowed and denied S3 operations using an EC2 IAM role.

### Validation

* Successfully launched an Amazon Linux 2023 EC2 instance.
* Successfully associated `ChurchEC2S3Role` with the EC2 instance.
* Verified role assumption using `aws sts get-caller-identity`.
* Verified temporary credentials through the EC2 Instance Metadata Service.
* Successfully listed objects in the S3 bucket.
* Successfully uploaded an object to S3.
* Confirmed that unauthorized `DeleteObject` operations were denied.

---

## [2026-08-06] – IAM Users, Groups & Least-Privilege Implementation

### Added

* Customer-managed IAM policy for the Media Team.
* `Media-Team` IAM group.
* `media-volunteer` IAM user.
* IAM group-based access control lab.
* IAM policy JSON configuration.
* IAM user and group lab documentation.

### Security Concepts

* Learned IAM policy structure and evaluation logic.
* Practiced authentication vs authorization.
* Learned explicit deny and implicit deny.
* Applied the Principle of Least Privilege.
* Compared individual user permissions with group-based permission management.
* Learned the security risks associated with long-lived IAM access keys.

### Validation

* Added the `media-volunteer` user to the `Media-Team` group.
* Verified inherited permissions from the group policy.
* Configured a dedicated AWS CLI profile.
* Successfully uploaded and downloaded an S3 object.
* Confirmed unauthorized object deletion was denied.
* Confirmed the user could not perform `s3:ListAllMyBuckets` without the required permission.

---

## [2026-08-01] – Amazon S3 Fundamentals

### Added

* First S3 bucket: `skyshield-abiodun-2026`.
* S3 configuration and security documentation.
* S3 architecture documentation.
* S3 production deployment documentation.
* S3 CLI command reference.
* S3 lifecycle policy configuration.

### Security Controls

* Enabled S3 Block Public Access.
* Enabled default server-side encryption using SSE-S3.
* Enabled bucket versioning.
* Configured S3 lifecycle management.
* Applied bucket tagging.
* Verified bucket configuration and access controls.

### Skills

* S3 bucket and object management.
* Bucket-level vs object-level permissions.
* S3 access control.
* S3 storage classes.
* S3 lifecycle policies.
* S3 versioning.
* S3 encryption.
* AWS CLI S3 operations.

---

## [2026-07-31] – Project Environment & AWS Foundation

### Added

* Project SkyShield Git repository.
* Local Cloud Security Journey project structure.
* GitHub repository and remote configuration.
* AWS CLI environment.
* Python development environment.
* Terraform installation and configuration.
* Project documentation structure.

### AWS Account Security

* Created AWS account.
* Enabled root account MFA.
* Created IAM administrator user.
* Enabled IAM user MFA.
* Configured AWS CLI authentication.
* Created AWS Budget.
* Enabled AWS Cost Anomaly Detection.

### Project Structure

* Created AWS learning directory structure.
* Added documentation directories for AWS services.
* Added command reference documentation.
* Added roadmap and progress tracking.
* Added weekly learning journal structure.

---

## Project Security Principles

Throughout the journey, the project has progressively adopted the following security principles:

* Principle of Least Privilege
* Strong authentication
* MFA for privileged identities
* Avoidance of unnecessary long-lived credentials
* Role-based access for workloads
* Temporary credentials where possible
* Explicit permission management
* Secure-by-default AWS configurations
* Continuous validation of access permissions
* Documentation of security decisions

---

## Current Status

Project SkyShield has progressed from basic AWS account setup and S3 fundamentals into **IAM identity security, role-based access control, temporary credentials, and EC2 workload identity**.

The next major focus is **EC2 and VPC networking fundamentals**, followed by network security, AWS security monitoring services, automation, and cloud security engineering projects.
