# Validation Screenshots (AWS Console + Terminal Evidence)

This document contains screenshots as proof that my secure access flow is working properly.
I captured these while building the setup in AWS console and doing the testing from terminal.

**Access path I followed:**
Laptop → Bastion Host (Public Subnet, AZ A) → PostgreSQL Client EC2 (Private Subnet, AZ B) → Amazon RDS PostgreSQL

I added screenshots for each major part so it’s easy to check what I configured and why.

For every step below, I’m including:
- Where to click in AWS Console (so someone can re-check it)
- What the screenshot is showing
- What it confirms (basically why this proves security is correct)

---

## 1) VPC (Network Boundary)

### AWS Console
VPC → Your VPCs → select your VPC

- AWS Console → **VPC**
- Left menu → **Your VPCs**
- Click my VPC name (the one I created for this project)

### Screenshot should show
- VPC name and VPC ID
- IPv4 CIDR block (example like 10.0.0.0/16)
- DNS settings (DNS hostnames / DNS resolution) if it’s visible on the page

### What this proves / why this matters
- This confirms the whole setup is inside a **custom VPC** 
- VPC CIDR is the main network boundary, and all public/private subnets are created inside this
- Having DNS enabled helps with things like resolving the **RDS endpoint** from the private EC2 (otherwise connection becomes messy)

📸 Screenshot:

<img width="852" height="327" alt="image" src="https://github.com/user-attachments/assets/15e9dcb8-ab10-4da2-9b2a-1deb28f8ebc7" />


---

## 2) Subnets (Public vs Private + AZ placement)

### 2.1 Public Subnet (AZ A)

#### AWS Console clicks
VPC → Subnets → select the public subnet

#### Screenshot should show
- Subnet name
- Availability Zone = AZ A
- CIDR block
- “Auto-assign public IPv4” (if visible)

#### What it proves
- Bastion host is placed in a public subnet designed for internet ingress

📸 Screenshot:

<img width="940" height="407" alt="image" src="https://github.com/user-attachments/assets/5bb4e256-c149-4d94-bff7-ef94e540f5b2" />



---

### 2.2 Private Subnet (AZ B)

#### AWS Console
VPC → Subnets → select the private subnet

#### Screenshot should show
- Subnet name
- Availability Zone = AZ B
- CIDR block
- “Auto-assign public IPv4” disabled (if visible)

#### What it proves
- PostgreSQL client EC2 and RDS are placed in a private subnet (no public IP exposure)

📸 Screenshot:

<img width="856" height="384" alt="image" src="https://github.com/user-attachments/assets/e8265537-7060-4008-883d-b19a15f86a3a" />


---

## 3) Internet Gateway (IGW) for Public Subnet Access

### AWS Console clicks
VPC → Internet Gateways → select your IGW

### Screenshot should show
- IGW attached to the correct VPC

### What it proves
- Public subnet can reach the internet through IGW (needed for bastion SSH access)

📸 Screenshot:

<img width="940" height="241" alt="image" src="https://github.com/user-attachments/assets/486e4014-c6bc-4d9e-b7da-f9b556ad639d" />



---

## 4) Route Tables (Public subnet uses IGW; Private subnet uses NAT)

### 4.1 Public Route Table

#### AWS Console clicks
VPC → Route Tables → select the public route table → Routes tab

#### Screenshot should show
- Route: `0.0.0.0/0 → Internet Gateway (igw-...)`

#### What it proves
- Public subnet has an internet route enabling SSH into bastion from laptop

📸 Screenshot:

<img width="940" height="271" alt="image" src="https://github.com/user-attachments/assets/f3f1acdb-f6a4-453a-94af-caac8060c5b1" />


---

## 5) NAT Gateway (Outbound Internet for Private Subnet)

> NAT Gateway is used so the PostgreSQL client EC2 (in the private subnet)
can download and install the PostgreSQL client package, without exposing the instance
to inbound internet traffic. 

### 5.1 NAT Gateway resource

#### AWS Console
VPC → NAT Gateways → select your NAT Gateway

#### Screenshot should show
- NAT Gateway state = Available
- Located in the public subnet
- Elastic IP associated

#### What it proves
- NAT is correctly deployed in the public subnet to provide outbound internet access for private resources

📸 Screenshot:

<img width="940" height="302" alt="image" src="https://github.com/user-attachments/assets/85f026cb-2f4f-4649-b56f-63deba9ea569" />


---

### 5.2 Private Route Table routes via NAT

#### AWS Console
VPC → Route Tables → select the private route table → Routes tab

#### Screenshot should show
- Route: `0.0.0.0/0 → NAT Gateway (nat-...)`
- No route to Internet Gateway

#### What it proves
- Private subnet instances can reach the internet outbound (for updates/package installs)
- Private subnet still cannot accept inbound internet traffic

📸 Screenshot:

<img width="940" height="274" alt="image" src="https://github.com/user-attachments/assets/01fe4be6-10b9-4df1-bf11-53f2a9625411" />


---

## 6) Security Groups (Least Privilege)

<img width="940" height="143" alt="image" src="https://github.com/user-attachments/assets/9a2db392-a901-4c26-bf20-fab3cba64a77" />


### 6.1 Bastion Security Group (SSH only from Laptop)

#### AWS Console
EC2 → Security Groups → select Bastion SG → Inbound rules

#### Screenshot should show
- Inbound: SSH (22)
- Source: laptop public IP `/32`

#### What it proves
- Bastion is tightly locked down and accessible only from a trusted client

📸 Screenshot:

<img width="1637" height="497" alt="image" src="https://github.com/user-attachments/assets/4735c11b-6c74-4214-855a-9461defeb54a" />


---

### 6.2 PostgreSQL Client EC2 Security Group (SSH only from Bastion SG)

#### AWS Console clicks
EC2 → Security Groups → select PG Client SG → Inbound rules

#### Screenshot should show
- Inbound: SSH (22)
- Source: Bastion security group (SG reference)

#### What it proves
- Private EC2 is not accessible directly; only reachable via bastion host

📸 Screenshot:

<img width="1631" height="533" alt="image" src="https://github.com/user-attachments/assets/0986cf29-d052-4fc2-87b6-382532bd00ef" />


---

### 6.3 RDS Security Group (5432 only from PG Client SG)

#### AWS Console
EC2 → Security Groups → select RDS SG → Inbound rules
(or RDS → Databases → select DB → Connectivity & security → VPC security groups)

#### Screenshot should show
- Inbound: PostgreSQL (5432)
- Source: PG Client EC2 security group (SG reference)

#### What it proves
- Database is accessible only from the approved private EC2 tier

📸 Screenshot:

<img width="1627" height="518" alt="image" src="https://github.com/user-attachments/assets/04a1a87c-e04c-4d46-a013-c40779332785" />

---

## 7) EC2 Instances (Subnet placement + exposure)

### 7.1 Bastion Host EC2 details

#### AWS Console clicks
EC2 → Instances → select bastion instance → Details / Networking

#### Screenshot should show
- Subnet = Public subnet (AZ A)
- Public IPv4 present
- Security group = Bastion SG

#### What it proves
- Bastion is the only internet-reachable compute resource

📸 Screenshot:

<img width="940" height="359" alt="image" src="https://github.com/user-attachments/assets/672370ca-48ce-4490-9b71-8742d4179010" />


---

### 7.2 PostgreSQL Client EC2 details

#### AWS Console clicks
EC2 → Instances → select PG client instance → Details / Networking

#### Screenshot should show
- Subnet = Private subnet (AZ B)
- No public IPv4 address
- Security group = PG Client SG

#### What it proves
- PG Client EC2 is private and can only be reached through bastion

📸 Screenshot:

<img width="940" height="364" alt="image" src="https://github.com/user-attachments/assets/7fc6b57e-e06a-4a39-84a7-4aed00318878" />


---

## 8) RDS PostgreSQL (Private + Not Publicly Accessible)

### AWS Console clicks
RDS → Databases → select your PostgreSQL DB → Connectivity & security

### Screenshot should show
- Publicly accessible = No
- VPC/security group attached
- Subnet group (private)

### What it proves
- RDS is private and not exposed to the internet

📸 Screenshot:

<img width="2270" height="1079" alt="image" src="https://github.com/user-attachments/assets/e872a6f2-c3f2-4503-b5c4-768751855ae3" />


---

## 9) Terminal Proof (SSH + DB connectivity)

The following terminal validation steps are already present in the project document:
- SSH to bastion
- SSH from bastion to private EC2
- Install PostgreSQL client
- Connect to RDS and create/query a table 

### 9.1 SSH Laptop → Bastion (Success)

#### Command
ssh -i <key.pem> ec2-user@<bastion-public-ip>

#### What it proves
- Bastion SSH access is working and restricted appropriately

📸 Screenshot:

<img width="940" height="144" alt="image" src="https://github.com/user-attachments/assets/a617a493-85d4-4674-ba2b-1bcb327f0baa" />

<img width="740" height="284" alt="image" src="https://github.com/user-attachments/assets/7839a688-b692-4410-b390-7449dab528b8" />


---

### 9.2 SSH Bastion → PG Client (Success)

#### Command
ssh -i <key.pem> ec2-user@<pg-client-private-ip>

#### What it proves
- Private EC2 accepts SSH only from bastion host

📸 Screenshot:

<img width="940" height="222" alt="image" src="https://github.com/user-attachments/assets/513ad197-f90f-49ad-893e-2e66b2e5f201" />


---

### 9.3 Install PostgreSQL client on Private EC2 (Uses NAT)

#### command
sudo yum install postgresql -y


#### What it proves
- Private EC2 can reach package repos outbound using NAT (no inbound exposure)

<img width="940" height="99" alt="image" src="https://github.com/user-attachments/assets/d7376ddd-4ce8-4c30-b85f-153d01c571fd" />

---

### 9.4 Connect to RDS + Create Table (Success)

#### Command
psql -h <rds-endpoint> -U <user> -d <db>

#### What it proves
- End-to-end DB connectivity works (private EC2 → RDS on 5432) 
📸 Screenshot:

<img width="940" height="312" alt="image" src="https://github.com/user-attachments/assets/c7e538a0-6fe5-40ea-b2e6-31a4c0bf17c4" />

---

### 9.5 Query Table (Success)

#### What it proves
- DB read/write operations work successfully
📸 Screenshot:

<img width="940" height="312" alt="image" src="https://github.com/user-attachments/assets/86b48d7f-d72f-4bc3-b605-bf03f75cceea" />

<img width="579" height="513" alt="image" src="https://github.com/user-attachments/assets/eb3af86d-2700-4943-81a0-429f52b1602b" />


---

## Final Conclusion
These screenshots confirm:
- Only bastion is internet-facing (SSH restricted to laptop IP)
- Private EC2 is reachable only via bastion (SSH restricted by SG reference)
- RDS is reachable only from private EC2 (5432 restricted by SG reference)
- NAT Gateway provides outbound internet for private EC2 to install PostgreSQL client without allowing inbound internet traffic
