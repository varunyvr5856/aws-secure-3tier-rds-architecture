# Validation & Testing

This folder documents the tests performed to validate the security
and connectivity of the architecture.

## Validation Objectives
- Confirm private EC2 is not reachable from the internet
- Ensure SSH access is possible only through the bastion host
- Verify PostgreSQL connectivity from private EC2 to RDS

## Validation Summary

### Test 1: SSH Access to Bastion Host
Result: Successful

### Test 2: SSH from Bastion to Private EC2
Result: Successful

### Test 3: PostgreSQL Connection from Private EC2 to RDS
Result: Successful

## Conclusion
The architecture enforces the intended access path:
Laptop → Bastion Host → Private EC2 → Amazon RDS
