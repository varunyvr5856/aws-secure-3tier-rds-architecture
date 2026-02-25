# Architecture Overview

This folder contains diagrams that explain the high-level design and network flow
of the secure 3-tier AWS architecture used in this project.

## Architecture - High level
- Shows the overall VPC layout
- Public subnet hosting the Bastion Host
- Private subnets hosting PostgreSQL Client EC2 and Amazon RDS
- Security boundaries enforced using security groups

## Network Flow
- Illustrates the allowed access path:
  Laptop → Bastion Host → Private EC2 → RDS
- Highlights permitted ports:
  - SSH (22)
  - PostgreSQL (5432)

## Design Goals
- No direct internet access to private resources
- Single controlled entry point (bastion host)
- Least-privilege network access

## 3 tier architecture diagram
<img width="1211" height="561" alt="aws-3tier architecture drawio (1)" src="https://github.com/user-attachments/assets/e6b12526-928d-41f3-802f-501912a503f1" />
