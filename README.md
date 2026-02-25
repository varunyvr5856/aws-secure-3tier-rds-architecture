# Secure 3‑Tier AWS Architecture (Console Implementation)
Secure 3-tier AWS architecture using Bastion Host, Private EC2, and Amazon RDS

## Overview
This project demonstrates a **secure, network‑isolated 3‑tier architecture** on AWS using a **bastion host pattern** to access private resources. The goal is to ensure that:

- **Only the bastion host is reachable from the internet**
- The **PostgreSQL client EC2** instance stays **private** (no public IP)
- **Amazon RDS PostgreSQL** is **private** and accepts traffic **only** from the PostgreSQL client EC2 security group
- Access control follows **least privilege** using **security group–to–security group** rules

**Core access path:**
Laptop → Bastion Host (Public) → PostgreSQL Client EC2 (Private) → RDS PostgreSQL (Private)
<img width="742" height="160" alt="Network flow drawio" src="https://github.com/user-attachments/assets/7e41f7d3-09eb-48f2-a8f1-089b66ee57d6" />

## Architecture Summary
### AWS core  components
- **VPC (Custom)** with public and private subnets
- **Public Subnet** (Bastion Host EC2)
- **Private Subnet** (PostgreSQL Client EC2 + RDS)
- **Route Tables** and **Internet Gateway (IGW)** for public subnet access
- **Security Groups** enforcing strict inbound rules
- **Amazon RDS PostgreSQL** (not publicly accessible)

## Architecture Diagram
<img width="1211" height="561" alt="aws-3tier architecture drawio (1)" src="https://github.com/user-attachments/assets/dc43475f-3613-4c38-bf5d-b40f8dd7e262" />
(User Laptop's Public IP (what's my public IP) → Bastion Host → PostgreSQL Client EC2 → Amazon RDS)
 

## Key Security Principles
- Network isolation using private subnets
- Least privilege using security groups
- No public IPs for application or database tiers

### Availability Zones (AZ)
Resources are distributed across different AZs to reflect best practice design and to support future high availability improvements.

## Implementation Method
All resources were provisioned manually using the AWS Console to demonstrate
hands‑on understanding of AWS networking and security concepts.

## Validation
Connectivity and access controls were validated using SSH and PostgreSQL client
tests. Only the intended access path is permitted.

