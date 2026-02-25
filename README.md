# Secure 3‑Tier AWS Architecture (Console Implementation)
Secure 3-tier AWS architecture using Bastion Host, Private EC2, and Amazon RDS

## Overview
This project demonstrates a secure 3‑tier architecture built entirely using the
AWS Management Console. The architecture follows AWS security best practices by
isolating private resources and enforcing least‑privilege network access.

The design ensures:
- No direct internet access to private EC2 or RDS
- Controlled administrative access via a bastion host
- Database access restricted to a single private EC2 instance

## Architecture Summary
- Custom VPC
- Public subnet hosting a Bastion Host EC2
- Private subnets hosting:
  - PostgreSQL Client EC2
  - Amazon RDS PostgreSQL
- Security Group–to–Security Group access control

## Architecture Diagram
<img width="1211" height="561" alt="aws-3tier architecture drawio (1)" src="https://github.com/user-attachments/assets/dc43475f-3613-4c38-bf5d-b40f8dd7e262" />
(architecture/architecturetop → Bastion Host → PostgreSQL Client EC2 → Amazon RDS

## Key Security Principles
- Network isolation using private subnets
- Least privilege using security groups
- No public IPs for application or database tiers

## Implementation Method
All resources were provisioned manually using the AWS Console to demonstrate
hands‑on understanding of AWS networking and security concepts.

## Validation
Connectivity and access controls were validated using SSH and PostgreSQL client
tests. Only the intended access path is permitted.

