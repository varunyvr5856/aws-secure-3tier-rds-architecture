# Security Design

This folder documents the security controls used in the architecture,
with a focus on least-privilege access and network isolation.

## Security Groups
Security groups are used as the primary network security mechanism.

- Bastion Host:
  - Allows SSH (22) only from the user's laptop IP
- PostgreSQL Client EC2:
  - Allows SSH (22) only from the Bastion Host security group
- Amazon RDS:
  - Allows PostgreSQL (5432) only from the PostgreSQL Client EC2 security group

## Why Security Group Referencing?
- Avoids hard-coded IP addresses inside the VPC
- Scales cleanly with additional instances
- Clearly enforces tier-to-tier trust

## Network ACLs
- Default NACLs were used
