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
- Public IPs change when instances change or at auto scaling unles if elastic IPs are assigned
- Avoids hard-coded IP addresses inside the VPC

## Network ACLs
- Default NACLs were used
## Reasoning
- Security groups already provide stateful filtering
- Using NACLs in addition would increase complexity without strong benefit
- In production, restrictive NACLs may be used for compliance

## Difference from Security Groups
- NACLs are stateless
- Security Groups are stateful
- NACLs apply at subnet level

