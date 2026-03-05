# 🚀 AWS Application Load Balancer using Terraform

## 📌 Project Overview

This project provisions an **AWS Application Load Balancer (ALB)** with:

* Custom VPC
* Public Subnets
* Internet Gateway
* Security Group
* EC2 Instance (Apache Installed)
* Target Group
* ALB Listener

Region: `ap-south-1`

---

## 🏗️ Architecture

EC2 → Target Group → Application Load Balancer → Internet

---

## ⚙️ Prerequisites

* Terraform installed
* AWS CLI configured
* AWS IAM user with required permissions

---

## 📂 Project Structure

```
aws-alb-terraform/
│── main.tf
│── outputs.tf
│── terraform.tfvars
```

---

## 🚀 Deployment Steps

```bash
terraform init
terraform plan
terraform apply
```

---

## 🌐 Access Application

After deployment, Terraform outputs:

```
load_balancer_dns = <ALB DNS>
```

Open the DNS in browser to access the application.

---

## 🧹 Destroy Resources

```bash
terraform destroy
```

---

## 🛠️ Technologies Used

* AWS
* Terraform
* EC2
* Application Load Balancer
* Apache HTTP Server

---
📌 Complete Terraform Code (Production-Ready Basic Setup)
🔹 main.tf
```bash
provider "aws" {
  region = "ap-south-1"
}

# ---------------- VPC ----------------
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
tags = {
    Name = "Main"
  }
}

# ---------------- Internet Gateway ----------------
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main.id
tags = {
    Name = "I_gw"
  }
}

# ---------------- Public Subnet 1 ----------------
resource "aws_subnet" "subnet1" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "ap-south-1a"
  map_public_ip_on_launch = true
tags = {
    Name = "subnet1"
  }
}

# ---------------- Public Subnet 2 ----------------
resource "aws_subnet" "subnet2" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.2.0/24"
  availability_zone       = "ap-south-1b"
  map_public_ip_on_launch = true
tags = {
    Name = "subnet2"
  }
}

# ---------------- Route Table ----------------
resource "aws_route_table" "rt" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }
tags = {
    Name = "rt"
  }
}

# Associate Route Table
resource "aws_route_table_association" "a1" {
  subnet_id      = aws_subnet.subnet1.id
  route_table_id = aws_route_table.rt.id
}

resource "aws_route_table_association" "a2" {
  subnet_id      = aws_subnet.subnet2.id
  route_table_id = aws_route_table.rt.id
}

# ---------------- Security Group ----------------
resource "aws_security_group" "alb_sg" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
tags = {
    Name = "sg_load_balancer"
  }
}

# ---------------- EC2 Instance ----------------
resource "aws_instance" "app" {
  ami                         = "ami-019715e0d74f695be"
  instance_type               = "t3.micro"
  subnet_id                   = aws_subnet.subnet1.id
  vpc_security_group_ids      = [aws_security_group.alb_sg.id]
  associate_public_ip_address = true

   user_data = base64encode(<<-EOF
    #!/bin/bash
    apt-get update -y
    apt-get install -y apache2
    systemctl start apache2
    systemctl enable apache2
    echo "Hello from Terraform ALB" > /var/www/html/index.html
    EOF
      )
tags = {
    Name = "app"
  }
}

# ---------------- Target Group ----------------
resource "aws_lb_target_group" "tg" {
  name     = "alb-target-group"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id
}

# ---------------- ALB ----------------
resource "aws_lb" "alb" {
  name               = "terraform-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb_sg.id]
  subnets            = [aws_subnet.subnet1.id, aws_subnet.subnet2.id]
tags = {
    Name = "ALB"
  }
}

# ---------------- Listener ----------------
resource "aws_lb_listener" "listener" {
  load_balancer_arn = aws_lb.alb.arn
  port              = 80
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.tg.arn
  }
}

# Attach EC2 to Target Group
resource "aws_lb_target_group_attachment" "attach" {
  target_group_arn = aws_lb_target_group.tg.arn
  target_id        = aws_instance.app.id
  port             = 80
}


```

🔹 outputs.tf
```bash
output "load_balancer_dns" {
  value = aws_lb.alb.dns_name
}
```

🚀 Step 4: Initialize & Deploy
```bash
terraform init
terraform plan
terraform apply
```
After apply, Terraform will output:
**load_balancer_dns = http://xxxx.ap-south-1.elb.amazonaws.com**
