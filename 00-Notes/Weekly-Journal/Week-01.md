## Week 01 Reflection
Topics Covered
-- AWS account setup and security best practices
-- Multi-Factor Authentication (MFA)
-- AWS CLI installation and configuration
-- Amazon S3 fundamentals
-- S3 bucket creation and object management
-- Block Public Access
-- IAM Policies vs Bucket Policies
-- Server-Side Encryption
-- Versioning
-- Lifecycle Policies (concepts)
-- Production-style S3 security baseline

## Commands Learned
-- aws configure
-- aws sts get-caller-identity
-- aws s3 ls
-- aws s3 cp
-- aws s3api get-bucket-location
-- aws s3api get-public-access-block
-- aws s3api get-bucket-encryption
-- aws s3api put-bucket-versioning
-- aws s3api get-bucket-versioning
-- aws s3api put-bucket-tagging
-- aws s3api get-bucket-tagging
-- Biggest Concepts I Learned

1. Security is layered

One of my biggest takeaways this week is that cloud security is built using multiple layers rather than relying on a single control.

For example:

-- IAM controls who can access resources.
-- Bucket Policies control access to the bucket itself.
-- Block Public Access prevents accidental public exposure.
-- Encryption protects stored data.
-- Versioning helps recover from accidental deletion.
-- Lifecycle Policies automate retention and cost optimization.

Each control addresses a different security concern.

2. Versioning is based on object keys

Initially, I assumed AWS compared file contents when creating versions.

I learned that versioning is determined entirely by the object key (name/path).

Uploading another object with the same key creates a new version regardless of whether the contents changed.

3. S3 objects are immutable

Another concept that changed my understanding was object immutability.

AWS does not modify an existing object.

Instead, it stores a completely new object with a new Version ID while preserving previous versions.

4. Security should follow business requirements

This week taught me that security decisions should be based on business needs.

Not every file requires the same level of protection.

For example:

-- Financial records may require SSE-KMS.
-- General documents may only require SSE-S3.
-- Lifecycle policies depend on retention requirements.

The business determines the requirements, and security controls should support them.

5. Think beyond commands

Earlier in the week, I focused mainly on learning AWS CLI commands.

As the week progressed, I found myself asking why AWS behaves the way it does rather than simply how to execute a command.

This shift helped me understand the design decisions behind features such as Versioning and Lifecycle Policies.

## Challenges I Encountered
-- Lost my AWS CLI profile after repairing my Windows boot configuration.
-- Initially struggled with AWS documentation because I wasn't familiar with the CLI command structure.
-- Encountered a command syntax error while applying bucket tags due to incorrect quotation marks.
-- Learned the difference between Windows command syntax and Linux examples shown in AWS documentation.

## How I Solved Them
-- Reconfigured the AWS CLI profile.
-- Used aws help to discover available commands instead of relying entirely on documentation.
-- Carefully read AWS error messages to identify syntax mistakes.
-- Verified every configuration after making changes.

## Production Lab Completed

This week I completed my first production-style AWS security deployment.

## Security controls implemented:

-- Block Public Access
-- Server-Side Encryption
-- Versioning
-- Resource Tagging

I also validated every configuration using the AWS CLI instead of assuming the commands worked.

## Biggest Personal Achievement

This week I realized that cloud security is not about memorizing AWS commands.

It is about understanding:

-- Why a security control exists.
-- What problem it solves.
-- How it supports business requirements.
-- How different security controls work together.

I believe this is the biggest shift in my thinking so far.

## Engineering Mindset Shift

This week I stopped thinking of AWS as a collection of commands and started thinking about the reasons behind its security features. Instead of asking, "How do I create a bucket?" I began asking, "Why was this feature designed this way, and what security problem does it solve?" That shift made me see cloud security as architecture and governance rather than configuration alone.

## Goals for Week 2
-- Learn Lifecycle Policies in depth.
-- Become comfortable writing JSON policy documents.
-- Understand IAM policies and policy evaluation.
-- Continue documenting my learning in Project SkyShield.
-- Build confidence using the AWS CLI without relying heavily on documentation.

## Final Reflection

When I started this journey, my focus was on creating AWS resources and learning commands.

By the end of this week, I found myself thinking more about architecture, governance, and security design. Instead of asking only "How do I do this?", I began asking "Why was it designed this way?" and "What problem does this feature solve?"

I also realized the importance of documenting both technical knowledge and personal reflections. My goal is for Project SkyShield to become more than a collection of labs—it should become a record of my growth into a Cloud Security Engineer.


## Questions That Changed My Thinking

-- Why doesn't AWS compare file contents for versioning?
-- What happens to a Delete Marker if no one restores the object?
-- Why doesn't AWS automatically delete old versions?
-- Why are users charged for multiple versions of the same object?
-- Why does AWS use immutable objects instead of updating files in place?