# Architecture Overview

This folder contains diagrams that explain the high-level design and network flow
of the secure 3-tier AWS architecture used in this project.

## Architecture Diagram
- Shows the overall VPC layout
- Public subnet hosting the Bastion Host
- Private subnets hosting PostgreSQL Client EC2 and Amazon RDS
- Security boundaries enforced using security groups

## Network Flow Diagram
- Illustrates the allowed access path:
  Laptop → Bastion Host → Private EC2 → RDS
- Highlights permitted ports:
  - SSH (22)
  - PostgreSQL (5432)

## Design Goals
- No direct internet access to private resources
- Single controlled entry point (bastion host)
- Least-privilege network access
