# EC2 Security Concepts

## What is EC2 Security?

EC2 security is the combination of AWS controls that protect virtual servers from unauthorized access while ensuring workloads can securely communicate with other AWS services.

EC2 security answers two questions:

- Who is allowed to reach this instance?
- What is this instance allowed to access?

AWS protects EC2 using multiple independent security layers rather than relying on a single control.

---

# EC2 Security Layers

A secure EC2 deployment combines several AWS services.

```text
                EC2 Security Layers

        Internet
            │
            ▼
    Internet Gateway
            │
            ▼
      Route Table
            │
            ▼
    Network ACL
            │
            ▼
 Security Group
            │
            ▼
     EC2 Instance
            │
            ▼
      IAM Role
            │
            ▼
   Temporary Credentials
```

Each layer provides a different type of protection.

---

# Security Groups

Security Groups are EC2's primary workload firewall.

Current lab Security Group:

```text
launch-wizard-1
```

Current inbound rule:

| Port | Source |
|------|--------|
| TCP 22 | `105.113.128.179/32` |

Outbound traffic is currently unrestricted.

---

# Security Group Characteristics

Security Groups are:

- Stateful
- Attached to ENIs
- Allow-based
- Capable of referencing other Security Groups

Unlike traditional firewalls, Security Groups automatically allow return traffic for established connections.

---

# Stateful Connections

Example:

```text
Laptop
   │ SSH
   ▼
EC2
```

When EC2 responds:

```text
EC2
 │
 ▼
Laptop
```

No additional outbound rule is required because Security Groups remember established sessions.

This behavior is called **stateful filtering**.

---

# Security Group References

Production environments should reference Security Groups rather than individual IP addresses.

Example:

```text
Web-SG
   │ HTTPS
   ▼
App-SG
   │ PostgreSQL
   ▼
DB-SG
```

Benefits include:

- Automatic support for changing IP addresses.
- Easier workload scaling.
- Cleaner trust relationships.

This pattern was reinforced during the multi-tier architecture exercises.

---

# Public Exposure Model

An EC2 instance is **not** automatically exposed simply because it has a public IP.

Internet access requires multiple conditions.

```text
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Route Table
    │
    ▼
Security Group
    │
    ▼
EC2
```

All layers must permit the traffic before a connection succeeds.

---

# Public vs Private Instances

| Public Instance | Private Instance |
|----------------|------------------|
| Public subnet | Private subnet |
| Route to IGW | Route to NAT Gateway |
| Public IP | Private IP |
| Internet reachable | Outbound only |

During the lab, the current EC2 instance was located in a public subnet with a dynamically assigned public IPv4 address.

---

# IAM Roles for EC2

EC2 workloads should authenticate using IAM Roles rather than long-lived access keys.

Architecture:

```text
EC2
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
```

This eliminates the need to store permanent AWS credentials on the server.

---

# IMDSv2 Security

EC2 retrieves temporary credentials through the Instance Metadata Service.

Metadata endpoint:

```text
169.254.169.254
```

IMDSv2 introduces a token-based authentication flow.

```text
Application
      │
      ▼
Request Token
      │
      ▼
IMDSv2
      │
      ▼
Temporary Credentials
```

Compared to IMDSv1, IMDSv2 provides stronger protection against certain metadata abuse techniques, including some Server-Side Request Forgery (SSRF) attacks.

---

# Least Privilege for EC2

An EC2 instance should receive only the permissions required for its workload.

Example:

Allowed:

```text
s3:ListBucket
s3:GetObject
s3:PutObject
```

Avoid granting:

```text
s3:*
```

or unnecessary permissions such as:

```text
s3:DeleteObject
```

unless the workload genuinely requires deletion.

This reduces the blast radius if the workload is compromised.

---

# Defense in Depth

A secure EC2 deployment combines multiple independent controls.

```text
Route Tables
      +
Network ACLs
      +
Security Groups
      +
IAM Role
      +
IMDSv2
      +
Least Privilege
      =
Layered Protection
```

If one layer is misconfigured, the remaining layers continue providing protection.

---

# Common EC2 Attack Surfaces

Potential risks include:

- Open SSH access (`0.0.0.0/0`)
- Unnecessary public IP addresses
- Excessive IAM permissions
- Long-lived access keys
- Disabled IMDSv2
- Broad outbound permissions
- Poor Security Group design

Most of these risks can be reduced through least privilege and layered security controls.

---

# EC2 Hardening Practices

Production EC2 instances should follow practices such as:

- Restrict SSH to trusted IP addresses.
- Prefer Session Manager over public SSH where possible.
- Use IAM Roles instead of access keys.
- Enable IMDSv2.
- Remove unnecessary inbound rules.
- Keep operating systems updated.
- Encrypt EBS volumes.
- Monitor workloads using CloudTrail and CloudWatch.

These practices significantly reduce the attack surface of an EC2 workload.

---

# Troubleshooting from a Security Perspective

A practical troubleshooting sequence is:

```text
Application
     │
     ▼
IAM Role
     │
     ▼
Security Group
     │
     ▼
Network ACL
     │
     ▼
Route Table
     │
     ▼
Internet Gateway
     │
     ▼
Destination
```

This layered approach prevents unnecessary troubleshooting at the wrong component.

---

# Production Best Practices

- Avoid exposing SSH to the Internet unnecessarily.
- Use Security Group references for application communication.
- Place databases in private subnets.
- Use IAM Roles for workloads.
- Enable IMDSv2.
- Follow least privilege.
- Use multiple Availability Zones for resilience.
- Monitor security events continuously.

---

# Key Takeaways

- Security Groups provide stateful workload protection.
- Network ACLs protect subnets, while Security Groups protect instances.
- Public IPs alone do not expose an EC2 instance.
- IAM Roles are preferred over long-lived credentials.
- IMDSv2 strengthens metadata security.
- Least privilege limits the impact of compromise.
- Defense in Depth combines multiple AWS security controls.
- Secure EC2 deployments rely on both networking and identity protections.
- EBS volumes persist independently of the running operating system.
- Troubleshooting should follow the network path instead of assuming a single component is responsible.