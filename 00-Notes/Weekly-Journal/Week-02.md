# Week 2 Reflection

## Week 2 Overview

This week significantly deepened my understanding of AWS Identity and Access Management (IAM). I moved beyond creating IAM Users and Groups and began working with IAM Roles, Trust Policies, Instance Profiles, AWS STS temporary credentials, and the EC2 Instance Metadata Service.

The major lesson from this week was that AWS identity management is not simply about granting permissions. It is about designing a secure authentication and authorization model that provides the minimum access required while reducing credential-management risks.

## Key Learning

One of the most important concepts I learned was the distinction between **Trust Policies and Permission Policies**.

A Trust Policy determines **who or what can assume an IAM Role**, while a Permission Policy determines **what that role can do after it has been assumed**. This helped me understand that successfully assuming a role does not automatically grant access to AWS resources. The identity must first be trusted to assume the role and must then have the appropriate permissions to perform the requested operation.

I also gained a deeper understanding of **authentication versus authorization**. In the EC2 lab, the instance was successfully authenticated through the IAM Role and AWS STS, but its authorization to access S3 was still determined by the permissions attached to the role.

## Practical Experience

I created the `ChurchEC2S3Role` IAM Role with a trust policy that allowed EC2 to assume it. I then created and attached a least-privilege S3 permission policy that allowed the instance to list the bucket, upload objects, and download objects.

I initially could not see the role when launching the EC2 instance because I had created the role using the AWS CLI without an Instance Profile. This led me to learn an important distinction between an IAM Role and an EC2 Instance Profile. I created the Instance Profile, associated the role with it, and successfully attached it to the EC2 instance.

The most valuable part of the lab was verifying that the EC2 instance could access S3 without having an AWS credentials file or manually configured access keys.

Running `aws sts get-caller-identity` from the EC2 instance showed that the workload was operating as an assumed role:

```text
arn:aws:sts::<account-id>:assumed-role/ChurchEC2S3Role/<session>
```

I also verified through `aws configure list` that the AWS CLI was obtaining credentials from `iam-role` rather than from a shared credentials file.

## Security Perspective

This exercise reinforced the Principle of Least Privilege. The EC2 instance was deliberately not granted `s3:DeleteObject` because deletion was not part of its intended function. When I attempted to delete an object, AWS returned `AccessDenied`, demonstrating implicit deny in practice.

I also learned why giving an EC2 instance broad permissions such as `AmazonS3FullAccess` is dangerous. If the instance or an application running on it is compromised, excessive permissions can significantly increase the attacker's ability to access, modify, exfiltrate, or destroy data.

The lab also introduced defense-in-depth concepts through the use of temporary credentials, IAM Roles, resource-level restrictions, and IMDSv2.

## Reflection

The biggest change in my understanding this week is that I am beginning to think about AWS security from the perspective of **risk and blast radius**, rather than simply asking whether an operation works.

For example, I now understand that the question is not:

> "Can this EC2 instance access S3?"

but rather:

> "What exactly does this EC2 instance need to do in S3, and how can I provide only those permissions while minimizing the impact if the workload is compromised?"

I also learned that security and operational efficiency can reinforce each other. Using IAM Roles allows permissions to be centrally managed and temporary credentials to be automatically handled, avoiding the operational burden and security risks associated with managing long-lived access keys across multiple workloads.

## Engineering Mindset Shift

This week I stopped measuring success by whether an AWS operation worked and started evaluating whether it was securely designed. Instead of asking, "Can this EC2 instance access S3?" I began asking, "What is the minimum access this workload actually needs, and how can I reduce the blast radius if it is ever compromised?" That mindset helped me connect IAM theory with real-world security engineering.

## Goals for Week 3

- Understand EC2 networking fundamentals.
- Learn CIDR planning and subnet calculations.
- Explore VPC architecture using real AWS resources.
- Understand Route Tables and Internet Gateways.
- Compare Security Groups with Network ACLs.
- Continue documenting architecture and security concepts in Project SkyShield.

## Key Takeaway

This week's work helped me connect IAM theory with a real AWS workload. I was able to create an IAM Role, establish a trust relationship, attach least-privilege permissions, associate the role with an EC2 instance through an Instance Profile, obtain temporary credentials through IMDS, and successfully access an S3 bucket without storing long-lived credentials on the instance.

This was an important step toward understanding how IAM is implemented in production AWS environments and strengthened my understanding of secure workload identity, least privilege, credential management, and defense in depth.
