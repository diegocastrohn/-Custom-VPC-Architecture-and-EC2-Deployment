# -Custom-VPC-Architecture-and-EC2-Deployment
This project demonstrates the design and deployment of a custom AWS networking environment using Amazon VPC.
The objective was to create a public cloud infrastructure by configuring networking components, routing, security controls, and deploying an Amazon EC2 Linux instance accessible through SSH.

---

## Architecture

The architecture includes:

- Custom VPC
- Public subnets across multiple Availability Zones
- Internet Gateway
- Public Route Table
- Security Group
- EC2 Linux Instance

---

# AWS Services Used

| Service----------| Purpose |
|------------------|
| Amazon VPC------ | Network isolation and IP addressing |
| Subnets ---------| Network segmentation |
| Internet Gateway | Internet connectivity |
| Route Tables ----| Traffic management |
| Security Groups -| Instance-level firewall |
| Amazon EC2 ------| Virtual machine deployment |

---

# Network Design

## VPC

| Parameter | Value -----|
|-----------|------------|
| Name -----| Diego-Cloud-VPC |
| CIDR -----| 10.0.0.0/16 |
| Region ---| us-east-1 |

---

## Subnets

| Subnet | Availability Zone | CIDR |
|-----------------|------------|-------------|
| Public-Subnet-A | us-east-1a | 10.0.1.0/24 |
| Public-Subnet-B | us-east-1b | 10.0.2.0/24 |

---

# Routing Configuration

Public Route Table:

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | Internet Gateway |

This configuration allows resources in public subnets to communicate with the Internet.

---

# Security Configuration

Security Group Rules:

## Inbound

| Protocol | Port | Source |
|----------|------|--------|
| SSH -----| 22 --| My Public IP |
| HTTP ----| 80 --| 0.0.0.0/0 |

## Outbound

| Protocol | Destination |
|----------|-------------|
| All Traffic| 0.0.0.0/0 |

---

# EC2 Deployment

Instance configuration:

| Parameter | Value |
|-----------|------|
| AMI ------| Amazon Linux 2023 |
| Instance Type | t3.micro |
| Private IP | 10.0.1.18 |
| Public IP | 18.215.34.17 |

---

# SSH Access Validation

Connection established using:

```bash
ssh -i Diego-Web-Key.pem ec2-user@18.215.34.17
