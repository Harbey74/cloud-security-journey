# AWS VPC Architecture

## 1. Overview

Amazon Virtual Private Cloud (VPC) is AWS's networking service that provides an isolated virtual network where AWS resources communicate securely.

A VPC allows organizations to define:

- The network address space.
- Network segmentation.
- Traffic routing.
- Internet connectivity.
- Private connectivity.
- High Availability across Availability Zones.

VPC architecture is primarily built around **CIDR blocks, subnets, route tables, gateways, DNS, and network segmentation**.

---

## 2. Core VPC Components

The major components covered in this project are:

```text
                    AWS VPC
                       │
      ┌────────────────┼────────────────┐
      │                │                │
   CIDR Block       Subnets       Route Tables
      │                │                │
      │                │                ├── Local Routes
      │                │                └── Internet Routes
      │                │
      │                ├── Public Subnet
      │                └── Private Subnet
      │
      └────────────────┼────────────────┐
                       │                │
                Internet Gateway   NAT Gateway
                       │
                       ▼
                    Internet
```

Each component performs a specific networking responsibility.

---

## 3. VPC CIDR Block

Every VPC begins with a CIDR block.

Current lab VPC:

| Property | Value |
|----------|-------|
| VPC | `vpc-06b79957438259a36` |
| CIDR | `172.31.0.0/16` |
| State | `available` |
| Tenancy | `default` |

A `/16` network provides:

- 16 network bits
- 16 host bits
- 65,536 IPv4 addresses

Subnet mask:

```text
255.255.0.0
```

The VPC CIDR becomes the address space from which every subnet is created.

---

## 4. Subnet Architecture

Subnets divide the VPC into smaller networks.

Current environment contains three subnets.

| Subnet | CIDR | Availability Zone |
|--------|------|-------------------|
| `subnet-0880ac2536f1400e4` | `172.31.0.0/20` | `eu-west-1c` |
| `subnet-0c78da000a4442dd9` | `172.31.16.0/20` | `eu-west-1a` |
| `subnet-031078f9020e8d737` | `172.31.32.0/20` | `eu-west-1b` |

Each `/20` subnet provides:

- 4096 total IPv4 addresses
- Approximately 4091 usable AWS addresses after AWS reserves five addresses.

Current EC2 belongs to:

```text
172.31.16.0/20
```

because its private IP:

```text
172.31.28.74
```

falls inside:

```text
172.31.16.0 – 172.31.31.255
```

---

## 5. Public and Private Subnets

Subnets become public or private based on routing.

### Public Subnet

```text
0.0.0.0/0
     │
     ▼
Internet Gateway
```

Characteristics:

- Can receive public IP addresses.
- Can communicate directly with the Internet.
- Suitable for web-facing resources.

### Private Subnet

```text
0.0.0.0/0
     │
     ▼
NAT Gateway
```

Characteristics:

- No direct inbound Internet access.
- Can initiate outbound Internet connections through NAT.
- Suitable for application servers.

### Database Subnet

```text
VPC CIDR
     │
     ▼
Local Routing Only
```

Characteristics:

- No Internet connectivity.
- Accessible only by trusted workloads inside the VPC.

---

## 6. Route Tables

Route Tables determine where network traffic is forwarded.

Current main route table:

| Property | Value |
|----------|-------|
| Route Table | `rtb-067579ab9ba4f9c22` |
| Main | Yes |

Current routes:

| Destination | Target |
|------------|--------|
| `172.31.0.0/16` | `local` |
| `0.0.0.0/0` | `igw-09bf9e73ae645ac28` |

Important distinction:

```text
Route Tables determine WHERE traffic goes.
They do not determine WHETHER traffic is allowed.
```

That responsibility belongs to Security Groups and Network ACLs.

---

## 7. Local Routing

Every VPC automatically creates a local route.

Current local route:

```text
172.31.0.0/16 → local
```

This route allows every subnet inside the VPC to communicate without requiring an Internet Gateway.

Example:

```text
EC2 A
   │
   ▼
Local Route
   │
   ▼
EC2 B
```

This behavior became important during the Security Group reference exercises.

---

## 8. Internet Gateway

The current VPC uses:

| Property | Value |
|----------|-------|
| Internet Gateway | `igw-09bf9e73ae645ac28` |
| State | `available` |

Traffic flow:

```text
EC2
 │
 ▼
Route Table
 │
 ▼
Internet Gateway
 │
 ▼
Internet
```

An Internet Gateway provides Internet connectivity but does not filter traffic.

---

## 9. NAT Gateway

Current observation:

```text
No NAT Gateway exists in the lab VPC.
```

Production architecture:

```text
Private EC2
     │
     ▼
Private Route Table
     │
0.0.0.0/0
     ▼
NAT Gateway
     │
     ▼
Internet Gateway
     │
     ▼
Internet
```

Important principle:

The NAT Gateway itself resides inside a public subnet while allowing private resources to initiate outbound Internet connections.

---

## 10. AWS DNS Architecture

The VPC provides built-in DNS services.

Current configuration:

| Setting | Value |
|---------|-------|
| DNS Support | Enabled |
| DNS Hostnames | Enabled |
| Resolver | `172.31.0.2` |

Inside the EC2 instance:

```text
nameserver 172.31.0.2
search eu-west-1.compute.internal
```

DNS flow:

```text
Application
      │
      ▼
172.31.0.2
      │
      ▼
AWS DNS Resolution
```

During the lab, the resolver successfully returned both IPv4 and IPv6 records for `example.com`.

---

## 11. IPv4 and IPv6 Architecture

The lab demonstrated an important networking distinction.

### IPv4

```bash
curl -4 https://example.com
```

Succeeded.

### IPv6

```bash
curl -6 https://example.com
```

Failed.

The ENI currently has:

```text
Private IPv6: []
```

This demonstrates that DNS resolution alone does not guarantee network connectivity.

---

## 12. Availability Zones

The VPC currently spans three Availability Zones.

```text
eu-west-1a
eu-west-1b
eu-west-1c
```

Availability Zones are isolated physical data centers inside a Region.

Distributing workloads across multiple AZs improves resilience and fault tolerance.

---

## 13. High Availability Architecture

Single-AZ deployment:

```text
AZ-a
 ├── Web
 ├── App
 └── Database
```

Risk:

One Availability Zone failure can interrupt the entire application.

Multi-AZ deployment:

```text
Region
├── AZ-a
│   ├── Web
│   └── App
│
└── AZ-b
    ├── Web
    └── App
```

Traffic continues flowing even if one Availability Zone becomes unavailable.

---

## 14. Production Three-Tier Architecture

The production design developed during this milestone follows a three-tier architecture.

```text
                   INTERNET
                      │
                      ▼
             Internet Gateway
                      │
             Public Route Table
                      │
               Public Subnet
                      │
            Web Tier / Load Balancer
                      │
                      ▼
           Private Application Subnet
                      │
                Application EC2
                      │
                      ▼
            Private Database Subnet
                      │
                  Database
```

Responsibilities:

- Web Tier receives Internet traffic.
- Application Tier processes business logic.
- Database Tier remains isolated from the Internet.

This separation reduces the attack surface while maintaining functionality.

---

## 15. Complete VPC Architecture

The networking architecture demonstrated throughout the lab can be summarized as:

```text
                    AWS Region
                        │
                        ▼
                VPC 172.31.0.0/16
                        │
      ┌─────────────────┼─────────────────┐
      │                 │                 │
      ▼                 ▼                 ▼
Public Subnet     Public Subnet     Public Subnet
172.31.0.0/20     172.31.16.0/20    172.31.32.0/20
      │                 │                 │
      └───────────── Main Route Table ────┘
                        │
        ┌───────────────┴───────────────┐
        │                               │
        ▼                               ▼
Local Route                    Internet Gateway
172.31.0.0/16                  0.0.0.0/0
```

The EC2 instance communicates through:

- VPC
- Subnet
- Route Table
- Internet Gateway
- AWS DNS

before reaching external destinations.

---

## 16. Architecture Summary

The VPC architecture implemented in this project separates networking responsibilities across multiple AWS components.

```text
                NETWORK FOUNDATION
                      │
                 VPC CIDR Block
                      │
                      ▼
                   Subnets
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   Route Tables   DNS Services   Availability Zones
        │
        ▼
Gateways (IGW / NAT)
        │
        ▼
AWS Resources
```

The key architectural principle is to separate **network boundaries**, **traffic routing**, **connectivity**, and **workload placement** so that each networking component performs a clearly defined responsibility.