# **TechCorp Web Application Infrastructure – Terraform Deployment**

This repository contains the full Terraform configuration for deploying TechCorp’s high-availability web application infrastructure on AWS.
The setup includes a VPC, public and private subnets, NAT gateways, bastion host, web servers, database server, an Application Load Balancer (ALB), and all required networking and security components.

##  1. Project Overview**

TechCorp is launching a new web application that requires:

* High availability across multiple Availability Zones
* Public and private subnets for secure network isolation
* Application Load Balancer (ALB) for HTTP traffic
* Bastion host for secure SSH access
* Scalable architecture with NAT gateways and EC2 instances
* Separate security groups for web, bastion, and database servers

This Terraform configuration provisions the entire infrastructure automatically.

---

##  2. Architecture Diagram (Conceptual)**

```
                         Internet
                             |
                       Application Load Balancer
                     /                               \
            Public Subnet 1                     Public Subnet 2
           (AZ a)                                     (AZ b)
               |                                           |
       Bastion Host                                  NAT Gateway 2
       NAT Gateway 1                                      |
               |                              -------------------------
               |                             |                       |
        Private Subnet 1                Private Subnet 2         Private Subnet 1
       Web Server 1                     Web Server 2             Database Server
```

---

##  3. Repository Structure**

```
terraform-assessment/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars.example
│
├── user_data/
│   ├── web_server_setup.sh
│   └── db_server_setup.sh
│
└── README.md
```

---

##  4. Prerequisites**

Install the following on your system:

* **Terraform ≥ 1.0**
* **AWS CLI** configured with valid IAM credentials
* An **SSH key pair** uploaded to AWS EC2 (same name used in variables)
* Your **public IP address** (to allow SSH access into bastion)

---

## 5. What This Terraform Configuration Creates**

### **Networking**

* VPC (10.0.0.0/16)
* 2 public subnets
* 2 private subnets
* Internet Gateway
* NAT Gateways (one per public subnet)
* Route tables and subnet associations

### **Compute**

* Bastion host (Amazon Linux 2)
* Web Server 1 and Web Server 2 (Apache installed via user_data)
* Database server running PostgreSQL

### **Security**

* Bastion SG (SSH allowed only from your IP)
* Web SG (HTTP+HTTPS from internet, SSH from Bastion)
* Database SG (Postgres + SSH allowed only internally)

### **Load Balancing**

* Application Load Balancer (ALB)
* Target group with health checks
* Listener (port 80)

---

## 6. How to Deploy the Infrastructure**

### **STEP 1 — Clone the repository**

```sh
git clone https://github.com/<your-username>/month-one-assessment.git
cd month-one-assessment/terraform-assessment
```

### **STEP 2 — Update variable values**

Edit:

```
terraform.tfvars.example
```

Copy it into:

```
terraform.tfvars
```

Example:

```hcl
aws_region = "us-east-1"
key_pair_name = "my-keypair"
admin_ip = "YOUR.PUBLIC.IP.ADDRESS/32"

bastion_instance_type = "t3.micro"
web_instance_type     = "t3.micro"
db_instance_type      = "t3.small"

ssh_username = "techadmin"
ssh_password = "StrongPassword123"
```

### **STEP 3 — Initialize Terraform**

```sh
terraform init
```

### **STEP 4 — Validate**

```sh
terraform validate
```

### **STEP 5 — Preview Infrastructure**

```sh
terraform plan
```

### **STEP 6 — Deploy**

```sh
terraform apply
```

Type **yes** when prompted.

---

## 7. After Deployment – Outputs**

Terraform will return:

* **VPC ID**
* **Load Balancer DNS name**
* **Bastion Public IP**

Example:

```
alb_dns_name = techcorp-web-alb-xxxxx.us-east-1.elb.amazonaws.com
bastion_ip   = 3.92.44.21
```

---

## 8. How to Test Infrastructure**

### **1️⃣ Test Load Balancer**

Paste ALB DNS name into browser:

```
http://<alb-dns-name>
```

You should see:

```
Web Server 1 (or Web Server 2)
Instance ID: i-xxxxxxxxx
```

ALB will switch between the 2 servers.

---

### **2️⃣ SSH into Bastion Host**

```sh
ssh techadmin@<bastion-ip>
```

---

### **3️⃣ SSH into Web Servers (through Bastion)**

From bastion:

```sh
ssh techadmin@10.0.3.10   # Web Server 1
ssh techadmin@10.0.4.10   # Web Server 2
```

---

### **4️⃣ SSH into DB Server**

```sh
ssh techadmin@10.0.3.20
```

### **5️⃣ Connect to PostgreSQL**

Inside DB server:

```sh
sudo -i -u postgres
psql
```

---

## 9. Cleanup / Destroy Infrastructure**

To avoid AWS costs, remove everything:

```sh
terraform destroy
```

Type **yes**.

---

##  10. Evidence Required for Submission**

Place screenshots in:

```
evidence/
```

You must include:

* Terraform plan
* Terraform apply success
* AWS console showing:

  * VPC
  * Subnets
  * NAT gateways
  * EC2 instances
  * ALB
* Browser showing ALB serving from Web Server 1 and Web Server 2
* SSH access:

  * Into bastion
  * From bastion into web servers
  * From bastion into database
* PostgreSQL connection
* ALB URL serving page



## 11. Terraform State**

Include `terraform.tfstate` but **REMOVE ALL SENSITIVE INFORMATION**.


## 12. Author**

**TechCorp Cloud Engineering – Junior Engineer Assessment**
Prepared by: ISAAC PELUMI ELIJAH



