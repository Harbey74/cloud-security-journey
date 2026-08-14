# Week 3 Reflection

## Week 3 Overview

This week shifted my focus from **identity security** to **AWS networking**. I moved beyond simply launching an EC2 instance and began understanding how traffic actually moves through AWS using VPCs, subnets, route tables, Internet Gateways, NAT Gateways, Security Groups, Network ACLs, and AWS DNS.

The biggest lesson from this week was that AWS networking is built as a series of independent layers, where each component answers a different question. Instead of viewing an EC2 instance as a standalone server, I now understand it as part of a larger networking architecture where routing, filtering, identity, and connectivity work together.

## Key Learning

One of the most valuable concepts I learned was the difference between **where traffic goes** and **whether traffic is allowed**.

Initially, it was easy to think that a route table alone determined whether a connection would succeed. Through multiple troubleshooting scenarios, I learned that these responsibilities are intentionally separated:

- Route Tables determine where traffic is forwarded.
- Security Groups determine whether a workload accepts or initiates traffic.
- Network ACLs filter traffic at the subnet boundary.
- Internet Gateways provide Internet connectivity.
- NAT Gateways allow private workloads to initiate outbound Internet connections without exposing them to unsolicited inbound traffic.

This layered model fundamentally changed how I think about network troubleshooting.

## Practical Experience

I inspected my actual AWS environment instead of relying only on diagrams.

Using the AWS CLI, I examined:

- My default VPC (`172.31.0.0/16`).
- Three `/20` subnets across multiple Availability Zones.
- The main Route Table.
- The attached Internet Gateway.
- The default Network ACL.
- My EC2 instance's Elastic Network Interface (ENI).
- The root `gp3` EBS volume.

I also verified that my EC2 instance was using Amazon's built-in DNS resolver (`172.31.0.2`) and confirmed that both **DNS Support** and **DNS Hostnames** were enabled for the VPC.

One particularly interesting observation came from testing IPv4 and IPv6 connectivity.

`getent hosts example.com` returned both IPv4 and IPv6 records. However:

- `curl -4 https://example.com` succeeded.
- `curl -6 https://example.com` failed.

Inspecting the ENI revealed that the instance had no IPv6 address assigned, which helped me understand that successful DNS resolution does not automatically mean network connectivity exists.

## CIDR and Subnetting

Another major breakthrough this week was finally becoming comfortable with CIDR notation.

At first, CIDR felt abstract, but working through progressively harder exercises made the pattern much clearer.

For example, I learned to determine:

- Network bits
- Host bits
- Subnet masks
- Network addresses
- Broadcast addresses
- Usable IP ranges

By the end of the exercises, I could confidently calculate values such as:

- `/24`
- `/26`
- `/28`

and explain why my EC2 instance belonged to the `172.31.16.0/20` subnet.

This was one of the most satisfying moments of the week because CIDR had previously been one of the concepts I found confusing.

## Security Perspective

This week's networking labs reinforced the idea of **Defense in Depth**.

Instead of relying on a single security control, AWS combines multiple independent layers:

- VPC boundaries
- Subnets
- Route Tables
- Internet Gateways
- NAT Gateways
- Security Groups
- Network ACLs

I also learned an important distinction between Security Groups and Network ACLs.

Security Groups are **stateful**, meaning return traffic is automatically allowed for established connections.

Network ACLs are **stateless**, meaning both inbound and outbound rules must independently allow traffic, and rule order becomes extremely important because the first matching rule wins.

Working through several traffic scenarios—especially the Network ACL rule-order exercises—helped reinforce this behavior.

## Architecture Thinking

Toward the end of the week, I designed a production-style **three-tier AWS architecture** consisting of:

- A public web tier.
- A private application tier.
- An isolated database tier.

Rather than focusing only on functionality, I began thinking about why each workload belongs in a particular subnet and which components should be allowed to communicate with each other.

This felt much closer to real cloud architecture than simply launching individual EC2 instances.

## Reflection

The biggest shift in my thinking this week is that I now approach networking as a sequence of decisions rather than a collection of unrelated AWS services.

Instead of asking:

> "Why can't this EC2 instance connect?"

I now instinctively trace the path:

- Does the route exist?
- Is the Security Group allowing it?
- Is the Network ACL allowing it?
- Is there an Internet Gateway or NAT Gateway where appropriate?
- Does DNS resolve correctly?
- Does the interface actually have the required IP addressing?

This layered troubleshooting mindset makes complex networking problems feel much more structured and manageable.

## Engineering Mindset Shift

This week I stopped troubleshooting networking problems randomly and started tracing traffic through each AWS networking layer. Instead of jumping straight to the EC2 instance, I now think about the entire path a packet follows—from routing, to subnet boundaries, to workload-level filtering—which feels much closer to how real cloud engineers diagnose production issues.

## Goals for Week 4

- Build a custom VPC from scratch.
- Create public and private subnets.
- Configure Internet and NAT Gateways.
- Practice route table configuration.
- Launch a production-style three-tier architecture.
- Continue strengthening troubleshooting skills using real AWS scenarios.

## Key Takeaway

This week's work transformed my understanding of AWS networking. I learned how EC2 networking components fit together, became comfortable with CIDR calculations, explored VPC architecture using real AWS resources, verified DNS behavior, understood the differences between Security Groups and Network ACLs, and designed a production-style three-tier network architecture.

More importantly, I developed a systematic way of reasoning about cloud networking, which will become the foundation for building secure AWS environments in the next phase of my Cloud Security journey.