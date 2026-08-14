# AWS EC2 Architecture

## 1. Overview

Amazon Elastic Compute Cloud (EC2) is AWS's virtual server service that provides scalable compute resources inside a Virtual Private Cloud (VPC).

Rather than existing as a single object, an EC2 instance is built from several AWS resources that work together to provide compute, networking, storage, identity, and connectivity.

Understanding these relationships is essential for cloud security, troubleshooting, and infrastructure design.

---

## 2. Core EC2 Components

The major EC2 components covered in this project are:

```text
                    Amazon EC2
                        │
        ┌───────────────┼───────────────┐
        │               │               │
       AMI       Instance Type         ENI
        │               │               │
        │               │               ├── Private IP
        │               │               ├── Public IP
        │               │               ├── MAC Address
        │               │               └── Security Groups
        │
        └───────────────┐
                        ▼
                     EBS Volume
```

Each component performs a specific role and should not be viewed as interchangeable.

---

## 3. Amazon Machine Image (AMI)

An Amazon Machine Image (AMI) provides the operating system and initial software configuration used when launching an EC2 instance.

During this project, the instance was launched using:

- Amazon Linux 2023
- AWS-optimized operating system
- Preconfigured EC2 integrations

The AMI determines the initial software environment, but it does not determine the compute resources available to the instance.

---

## 4. Instance Types

Instance Types determine the compute capacity assigned to an EC2 instance.

Current lab instance:

| Property | Value |
|----------|-------|
| Instance Type | `t3.micro` |

The instance type controls:

- CPU allocation
- Memory allocation
- Performance characteristics
- Network performance
- Pricing

Changing the instance type allows resources such as RAM and CPU to be increased without rebuilding the entire server.

---

## 5. Elastic Network Interface (ENI)

Every EC2 instance communicates through an Elastic Network Interface.

Current ENI:

| Property | Value |
|----------|-------|
| ENI | `eni-0aebacde2e9c4872f` |
| Private IPv4 | `172.31.28.74` |
| Public IPv4 | Auto-assigned |
| MAC Address | `06:49:4b:45:cb:a9` |
| Status | `in-use` |

The ENI owns the networking identity of the instance.

Responsibilities include:

- Private IP addressing
- Public IP association
- MAC address
- Security Group attachment
- Network communication

Unlike the EC2 instance itself, an ENI can be detached and attached to another compatible instance.

---

## 6. Public and Private IP Architecture

The lab instance demonstrated two different address types.

| Address Type | Example |
|--------------|---------|
| Private IPv4 | `172.31.28.74` |
| Public IPv4 | Auto-assigned |

The private address is used for communication inside the VPC.

The public address provides Internet reachability when routing and security controls also permit access.

A key observation during the lab was that the automatically assigned public IPv4 changed after inspection, demonstrating why production workloads often use Elastic IPs when a persistent public address is required.

---

## 7. Elastic Block Store (EBS)

The instance uses an Amazon Elastic Block Store (EBS) volume as its root storage device.

Current root volume:

| Property | Value |
|----------|-------|
| Volume Type | `gp3` |
| Size | 8 GB |
| Device | `/dev/xvda` |
| State | `in-use` |

The root volume stores:

- Operating system
- Installed applications
- Configuration files
- System logs

Unlike temporary instance storage, EBS exists independently of the running operating system and can persist after the instance is stopped.

---

## 8. EC2 Networking Flow

A network request made by an application follows multiple AWS networking components before reaching its destination.

```text
Application
     │
     ▼
Security Group
     │
     ▼
Elastic Network Interface
     │
     ▼
Subnet
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

This layered flow became an important troubleshooting model throughout the networking labs.

---

## 9. Instance Metadata Service (IMDSv2)

Amazon EC2 provides the Instance Metadata Service Version 2 (IMDSv2).

Rather than storing permanent AWS credentials on the server, applications retrieve temporary credentials from the metadata service.

Metadata endpoint:

```text
169.254.169.254
```

IMDSv2 introduces a token-based session flow.

```text
Application
      │
      ▼
Request IMDSv2 Token
      │
      ▼
Metadata Service
      │
      ▼
Temporary Role Credentials
```

The metadata service provides:

- IAM Role credentials
- Instance metadata
- Region
- Availability Zone
- Network information

---

## 10. Current EC2 Architecture

The lab environment currently looks like this.

```text
                    AWS EC2
                       │
        ┌──────────────┼──────────────┐
        │              │              │
       AMI       t3.micro          ENI
        │              │              │
        │              │              ├── 172.31.28.74
        │              │              ├── Public IPv4
        │              │              ├── MAC Address
        │              │              └── Security Group
        │
        ▼
     Amazon Linux 2023
        │
        ▼
     gp3 Root Volume
```

The instance also retrieves temporary credentials through IMDSv2 using the IAM Role attached through its Instance Profile.

---

## 11. EC2 Lifecycle

An EC2 instance moves through multiple lifecycle states.

```text
Launch
   │
   ▼
Pending
   │
   ▼
Running
   │
 ┌─┴─────────┐
 │           │
 ▼           ▼
Stop      Reboot
 │
 ▼
Start
 │
 ▼
Running
 │
 ▼
Terminate
```

Important observations:

- Stopping preserves the EBS volume.
- Reboot keeps the same instance.
- Auto-assigned public IPv4 addresses can change after stop/start.
- Termination permanently removes the instance unless additional protections are configured.

---

## 12. Architecture Summary

The EC2 architecture demonstrated in this project separates multiple independent AWS components.

```text
                  Compute
                     │
         Instance Type (CPU/RAM)
                     │
                     ▼
               EC2 Instance
                     │
     ┌───────────────┼───────────────┐
     │               │               │
     ▼               ▼               ▼
   AMI             ENI             EBS
     │               │               │
     │               │               ├── Persistent Storage
     │               ├── Private IP
     │               ├── Public IP
     │               ├── MAC Address
     │               └── Security Groups
     │
     ▼
Operating System
```

The key architectural principle is that an EC2 instance is the combination of compute, networking, storage, identity, and routing components rather than a standalone virtual machine.