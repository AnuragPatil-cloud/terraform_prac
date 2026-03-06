# AWS ALB + Auto Scaling Infrastructure using Terraform

![Terraform](https://img.shields.io/badge/IaC-Terraform-623CE4?logo=terraform)
![AWS](https://img.shields.io/badge/Cloud-AWS-orange?logo=amazonaws)
![GitHub Repo stars](https://img.shields.io/github/stars/AnuragPatil-cloud/terraform_prac)
![GitHub forks](https://img.shields.io/github/forks/AnuragPatil-cloud/terraform_prac)
![License](https://img.shields.io/badge/license-MIT-blue)

---

# Project Overview

This project demonstrates how to build a **highly available and scalable AWS infrastructure using Terraform**.

The infrastructure automatically distributes incoming traffic across multiple EC2 instances using an **Application Load Balancer (ALB)** and dynamically scales instances using **Auto Scaling Groups**.

This setup is commonly used in **real DevOps production environments**.

---

# AWS Services Used

| Service                                                                  | Description                                        |
| ------------------------------------------------------------------------ | -------------------------------------------------- |
| ![VPC](https://img.icons8.com/color/48/000000/cloud.png)                 | Amazon VPC for isolated network                    |
| ![Subnet](https://img.icons8.com/color/48/000000/network.png)            | Public subnets across availability zones           |
| ![ALB](https://img.icons8.com/color/48/000000/load-balancer.png)         | Application Load Balancer for traffic distribution |
| ![EC2](https://img.icons8.com/color/48/000000/server.png)                | EC2 instances hosting the application              |
| ![AutoScaling](https://img.icons8.com/color/48/000000/cloud-storage.png) | Auto Scaling Group for dynamic scaling             |
| ![Security](https://img.icons8.com/color/48/000000/lock.png)             | Security Groups for network security               |

---

# Infrastructure Architecture

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
     EC2 Instance           EC2 Instance
```

---

# Project Structure

```
terraform-aws-alb-autoscaling
│
├── provider.tf
├── vpc.tf
├── alb.tf
├── autoscaling.tf
├── variables.tf
├── outputs.tf
└── README.md
```

---

# Prerequisites

Before running this project, install:

* Terraform
* AWS CLI
* AWS Account
* Git

---
### Configure AWS credentials:

Install AWS CLI on Ubuntu <br>
Download the aws cli bundle using below command

```
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Configure AWS

```
aws configure
```

---

# Terraform Installation (Ubuntu)

```
wget -O - https://apt.releases.hashicorp.com/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) \
signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com \
$(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt install terraform
```

Verify installation:

```
terraform -v
```

---

# Terraform Infrastructure Code

## Provider Configuration

```
provider "aws" {
  region = "ap-south-1"
}
```

---

# VPC Creation

```
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

---

# Public Subnets

```
resource "aws_subnet" "public1" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "ap-south-1a"
}

resource "aws_subnet" "public2" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "ap-south-1b"
}
```

---

# Application Load Balancer

```
resource "aws_lb" "alb" {
  name               = "devops-alb"
  internal           = false
  load_balancer_type = "application"
  subnets            = [
    aws_subnet.public1.id,
    aws_subnet.public2.id
  ]
}
```

---

# Target Group

```
resource "aws_lb_target_group" "tg" {
  name     = "web-target-group"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id
}
```

---

# Launch Template

```
resource "aws_launch_template" "web_template" {
  name_prefix   = "web-template"
  image_id      = "ami-xxxxxxxx"
  instance_type = "t3.micro"
}
```

---

# Auto Scaling Group

```
resource "aws_autoscaling_group" "asg" {

  desired_capacity = 2
  max_size         = 4
  min_size         = 2

  vpc_zone_identifier = [
    aws_subnet.public1.id,
    aws_subnet.public2.id
  ]

  target_group_arns = [
    aws_lb_target_group.tg.arn
  ]
}
```

---

# Deployment Steps


### Initialize Terraform

```
terraform init
```

---

### Validate Code

```
terraform validate
```

---

### Plan Infrastructure

```
terraform plan
```

---

### Deploy Infrastructure

```
terraform apply
```

Type:

```
yes
```

Terraform will automatically create:

* VPC
* Subnets
* Internet Gateway
* Application Load Balancer
* Target Group
* Launch Template
* Auto Scaling Group
* EC2 Instances

---

# Scaling Example

```
CPU Utilization > 70% → Launch New EC2 Instance
CPU Utilization < 30% → Terminate EC2 Instance
```

---

# Destroy Infrastructure

To delete all created resources:

```
terraform destroy
```

---

# Author

**Anurag**

DevOps Engineer
AWS | Terraform 
