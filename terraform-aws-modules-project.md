# Terraform AWS Infrastructure using Modules

![Terraform](https://img.shields.io/badge/IaC-Terraform-623CE4?logo=terraform)
![AWS](https://img.shields.io/badge/Cloud-AWS-orange?logo=amazonaws)
![GitHub](https://img.shields.io/badge/GitHub-Project-blue?logo=github)

---

# Project Overview

This project demonstrates how to provision a **scalable AWS infrastructure using Terraform Modules**.

The infrastructure includes:

* VPC
* Public Subnets
* Application Load Balancer
* Target Group
* Launch Template
* Auto Scaling Group
* EC2 Instances

Terraform Modules are used to organize the infrastructure into reusable components.

---

# Architecture Diagram

```
                Internet
                    │
                    ▼
           Internet Gateway
                    │
                    ▼
        Application Load Balancer
                    │
                    ▼
              Target Group
                    │
                    ▼
            Auto Scaling Group
                    │
        ┌───────────┴───────────┐
        ▼                       ▼
   EC2 Instance 1          EC2 Instance 2
```

---

# AWS Services Used

| Service                   | Purpose                            |
| ------------------------- | ---------------------------------- |
| Amazon VPC                | Creates an isolated network        |
| Internet Gateway          | Allows internet connectivity       |
| Amazon EC2                | Hosts the application              |
| Application Load Balancer | Distributes incoming traffic       |
| Target Group              | Routes traffic to EC2 instances    |
| Launch Template           | Defines EC2 configuration          |
| Auto Scaling Group        | Automatically scales EC2 instances |
| Security Groups           | Controls inbound/outbound traffic  |

---

# Project Structure

```
terraform-aws-modules-project
│
├── provider.tf
├── main.tf
├── variables.tf
├── outputs.tf
│
├── modules
│   │
│   ├── vpc
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   │
│   ├── alb
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   │
│   ├── autoscaling
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│
└── README.md
```

---

# Prerequisites

Before running this project install:

* Terraform
* AWS CLI
* Git
* AWS Account

---
### Install AWS CLI on Ubuntu

Download the aws cli bundle using below command
```
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

# Configure AWS Credentials

```
aws configure
```

Enter:

```
AWS Access Key
AWS Secret Key
Region (example: ap-south-1)
```

---

# Terraform Installation (Ubuntu)

```
wget -O - https://apt.releases.hashicorp.com/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

```
echo "deb [arch=$(dpkg --print-architecture) \
signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com \
$(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

```
sudo apt update
sudo apt install terraform
```

Verify installation:

```
terraform -v
```

---

# Step 1: Clone Repository

```
git clone https://github.com/AnuragPatil-cloud/terraform_prac.git
```

```
cd   terraform_prac/
cd terraform-aws-modules-project
```

---

# Step 2: Initialize Terraform

```
terraform init
```

This downloads required providers and modules.

---

# Step 3: Validate Terraform Code

```
terraform validate
```

---

# Step 4: Check Execution Plan

```
terraform plan
```

This shows what infrastructure Terraform will create.

---

# Step 5: Deploy Infrastructure

```
terraform apply
```

Type:

```
yes
```

Terraform will create:

* VPC
* Subnets
* Internet Gateway
* Application Load Balancer
* Target Group
* Launch Template
* Auto Scaling Group
* EC2 Instances

---

# Example Terraform Code

## Provider Configuration

```
provider "aws" {
  region = "ap-south-1"
}
```

---

# Root Module

```
module "vpc" {
  source   = "./modules/vpc"
  vpc_cidr = "10.0.0.0/16"
}

module "alb" {
  source  = "./modules/alb"
  vpc_id  = module.vpc.vpc_id
  subnets = module.vpc.public_subnets
}

module "autoscaling" {
  source            = "./modules/autoscaling"
  subnets           = module.vpc.public_subnets
  target_group_arn  = module.alb.target_group_arn
}
```

---

# Example VPC Module

```
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
}
```

---

# Example ALB Module

```
resource "aws_lb" "alb" {
  name               = "terraform-alb"
  load_balancer_type = "application"
  subnets            = var.subnets
}
```

---

# Example Auto Scaling Module

```
resource "aws_autoscaling_group" "asg" {

  desired_capacity = 2
  max_size         = 4
  min_size         = 2

  vpc_zone_identifier = var.subnets

  target_group_arns = [
    var.target_group_arn
  ]
}
```

---

# Destroy Infrastructure

To remove all resources created by Terraform:

```
terraform destroy
```

---

# Author

Anurag

DevOps Engineer
AWS • Terraform • Docker • Kubernetes • CI/CD
