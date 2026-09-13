# 01 — VPC & Networking

## Overview

This project implements a custom AWS VPC with public and private network segments, controlled internet access, a bastion host, and a private EC2 workload.

The infrastructure demonstrates AWS networking concepts including network segmentation, routing, NAT, secure administrative access, and instance-level security controls.

## Architecture

![VPC Architecture](architecture.png)


## Network

| Resource         | Configuration            |
| ---------------- | ------------------------ |
| VPC              | `10.0.0.0/16`            |
| Public Subnet    | `10.0.1.0/24`            |
| Private Subnet   | `10.0.2.0/24`            |
| Internet Gateway | Attached to VPC          |
| NAT Gateway      | Located in public subnet |
| Elastic IP       | Attached to NAT Gateway  |

## Routing

### Public Subnet

The public subnet uses a route table with:

```text
0.0.0.0/0 → Internet Gateway
```

This provides internet connectivity to the bastion host.

### Private Subnet

The private subnet uses a route table with:

```text
0.0.0.0/0 → NAT Gateway
```

This allows the private EC2 instance to initiate outbound internet connections without exposing it directly to the internet.

## Bastion Host

A public EC2 instance is deployed in the public subnet and used as a **bastion host** for secure administrative access to the private EC2 instance.

The bastion host has a public IP address and is the only instance directly accessible from the internet.

Administrative access follows this path:

```text
Administrator
     │
     │ SSH
     ▼
Bastion Host
     │
     │ SSH
     ▼
Private EC2
```

SSH access to the bastion host is restricted to my public IP address.

## Private EC2

The private EC2 instance is deployed into the private subnet without a public IP address.

It cannot be accessed directly from the internet.

Administrative access is provided through the bastion host, with the private EC2 Security Group allowing SSH traffic only from the bastion host's Security Group.

Outbound internet traffic from the private instance is routed through the NAT Gateway.

## Security

Security Groups were configured using a least-privilege approach.

### Bastion Host Security Group

* SSH (22) allowed only from my public IP address
* No unnecessary inbound ports opened

### Private EC2 Security Group

* SSH (22) allowed only from the Bastion Host Security Group
* No direct SSH access from the internet
* No public IP address

This creates a controlled access path into the private subnet rather than exposing the private instance directly.

## Screenshots

### VPC

![VPC](screenshots/vpc.png)

### Subnets

![Subnets](screenshots/subnets.png)

### Route Tables

![Route Tables](screenshots/route-tables.png)

### NAT Gateway

![NAT Gateway](screenshots/nat-gateway.png)

### Bastion Host

![Bastion Host](screenshots/bastion-host.png)

### Private EC2

![Private EC2](screenshots/private-ec2.png)

### Security Groups

![Security Groups](screenshots/security-groups.png)

## Key Technical Concepts

* VPC CIDR addressing
* Public and private subnets
* Route tables
* Internet Gateway
* NAT Gateway
* Bastion hosts
* SSH
* Security Group references
* Network segmentation
* Least-privilege access

