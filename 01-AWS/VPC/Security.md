# VPC Security Concepts

## What is VPC Security?

Amazon Virtual Private Cloud (VPC) security is AWS's layered networking security model used to protect cloud resources by controlling how traffic enters, leaves, and moves within a virtual network.

VPC security answers two questions:

- Where is traffic allowed to go?
- Which network paths should be restricted?

Unlike EC2 security, which protects individual workloads, VPC security primarily protects the network infrastructure surrounding those workloads.

---

# Network Segmentation

Network segmentation divides a VPC into smaller trust zones using subnets.

A secure architecture separates workloads based on their exposure level.

```text
Internet
   │
   ▼
Public Subnet
   │
   ▼
Private Application Subnet
   │
   ▼
Private Database Subnet
```

This limits unnecessary communication and reduces the impact of a compromised resource.

---

# Public vs Private Subnets

A subnet becomes public or private based on its route table.

## Public Subnet

```text
0.0.0.0/0
     │
     ▼
Internet Gateway
```

Characteristics:

- Can receive public IPv4 addresses.
- Supports Internet-facing workloads.
- Suitable for web servers and load balancers.

## Private Subnet

```text
0.0.0.0/0
     │
     ▼
NAT Gateway
```

Characteristics:

- Cannot receive unsolicited Internet traffic.
- Can initiate outbound Internet connections.
- Suitable for application servers.

## Isolated Private Subnet

```text
VPC CIDR
     │
     ▼
Local Routing Only
```

Characteristics:

- No Internet connectivity.
- Used for highly sensitive workloads such as databases.

---

# Route Tables and Security

Route tables determine where traffic is forwarded.

Current lab route table:

| Destination | Target |
|------------|--------|
| `172.31.0.0/16` | `local` |
| `0.0.0.0/0` | Internet Gateway |

Important principle:

> Route tables provide connectivity, not permission.

Even if a route exists, traffic can still be blocked by Security Groups or Network ACLs.

---

# Internet Gateway Security

The Internet Gateway provides Internet connectivity.

Current lab Internet Gateway:

```text
igw-09bf9e73ae645ac28
```

Traffic flow:

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
Subnet
```

The Internet Gateway itself performs no filtering.

Security controls must be implemented elsewhere.

---

# NAT Gateway Security

The current lab VPC contains no NAT Gateway.

A production NAT architecture looks like:

```text
Private EC2
     │
     ▼
Private Route Table
     │
     ▼
NAT Gateway
     │
     ▼
Internet Gateway
     │
     ▼
Internet
```

Security benefit:

- Allows outbound Internet access.
- Blocks unsolicited inbound Internet connections.

This protects private workloads while still allowing updates and package downloads.

---

# Security Groups vs Network ACLs

Both Security Groups and Network ACLs protect traffic, but they operate differently.

| Security Group | Network ACL |
|---------------|-------------|
| ENI-level | Subnet-level |
| Stateful | Stateless |
| Allow only | Allow and Deny |
| Return traffic automatic | Return traffic requires rules |

Together they create multiple security boundaries.

---

# Network ACLs

Current lab Network ACL:

```text
acl-0b350bc85d5ec35b1
```

Current inbound rules:

```text
100 ALLOW ALL
32767 DENY ALL
```

Current outbound rules:

```text
100 ALLOW ALL
32767 DENY ALL
```

---

# Stateless Filtering

Network ACLs are stateless.

Example:

```text
Laptop
   │ SSH
   ▼
EC2
```

If inbound traffic is allowed but outbound return traffic is not explicitly permitted, the connection fails.

Unlike Security Groups, Network ACLs evaluate both directions independently.

---

# Rule Order

Network ACL rules are processed from the lowest rule number upward.

Example:

```text
90  DENY SSH
100 ALLOW ALL
```

Result:

- Rule 90 matches first.
- Rule 100 is never evaluated.
- Connection is denied.

This behavior was reinforced during the Network ACL troubleshooting exercises.

---

# Security Group References

Security Groups can reference other Security Groups instead of IP addresses.

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

- Easier scaling.
- Cleaner trust relationships.
- Reduced administrative overhead.

This pattern became central to the production three-tier design.

---

# AWS DNS Security

Current VPC DNS configuration:

| Setting | Value |
|---------|-------|
| DNS Support | Enabled |
| DNS Hostnames | Enabled |

Current resolver:

```text
172.31.0.2
```

The EC2 instance successfully resolved:

```text
example.com
```

Important observation:

DNS resolution succeeded for IPv6 records even though IPv6 connectivity failed because the ENI had no IPv6 address assigned.

This demonstrates that:

```text
DNS Resolution ≠ Network Connectivity
```

---

# IPv4 and IPv6 Security

The lab demonstrated:

```bash
curl -4 https://example.com
```

Succeeded.

```bash
curl -6 https://example.com
```

Failed.

Reason:

- DNS returned IPv6 records.
- The instance lacked IPv6 addressing.
- No IPv6 path existed.

This highlights the importance of validating both addressing and routing when troubleshooting dual-stack environments.

---

# Defense in Depth

AWS networking security relies on multiple independent controls.

```text
VPC Boundary
      +
Subnets
      +
Route Tables
      +
Internet Gateway
      +
NAT Gateway
      +
Network ACLs
      +
Security Groups
      =
Layered Protection
```

No single control should be trusted alone.

---

# Network Troubleshooting Framework

A practical troubleshooting sequence developed during the labs.

```text
Application
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
Internet Gateway / NAT Gateway
     │
     ▼
Destination
```

This layered approach helps isolate failures more efficiently than troubleshooting randomly.

---

# Production Best Practices

- Place Internet-facing workloads in public subnets.
- Keep application servers in private subnets.
- Isolate databases from Internet access.
- Use NAT Gateways for outbound-only connectivity.
- Follow least privilege with Security Groups.
- Use Security Group references instead of fixed IP addresses.
- Keep Network ACL rules simple unless additional filtering is required.
- Distribute workloads across multiple Availability Zones.

---

# Key Takeaways

- Network segmentation creates security boundaries.
- Route tables determine where traffic travels.
- Internet Gateways provide connectivity but not filtering.
- NAT Gateways protect private workloads from unsolicited inbound traffic.
- Security Groups provide stateful workload protection.
- Network ACLs provide stateless subnet protection.
- Rule order matters for Network ACLs.
- DNS resolution and connectivity are separate concepts.
- Defense in Depth combines multiple AWS networking controls.
- Layered troubleshooting makes network issues easier to diagnose.
- CIDR planning determines future scalability.
- Multi-AZ design improves availability and reduces single points of failure.