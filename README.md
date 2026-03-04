# terraform_prac
# 🚀 Terraform Infrastructure as Code

![Terraform](https://img.shields.io/badge/IaC-Terraform-623CE4?logo=terraform&logoColor=white)
![Maintained](https://img.shields.io/badge/Maintained-Yes-success)
![License](https://img.shields.io/badge/License-MIT-blue)

Infrastructure provisioning and management using Terraform by HashiCorp.

This repository follows Infrastructure as Code (IaC) best practices to provision and manage cloud resources in a scalable, modular, and reusable way.

---

## 📌 Project Overview

This project includes:

- ✅ Modular Terraform structure  
- ✅ Environment-based configurations (Dev / QA / Prod)  
- ✅ Remote backend support  
- ✅ State management best practices  
- ✅ Secure variable handling  

---

## 🏗️ Project Structure
.
├── modules/<br>
│ ├── vpc/<br>
│ ├── ec2/<br>
│ └── security-groups/<br>
├── environments/<br>
│ ├── dev/<br>
│ ├── qa/<br>
│ └── prod/<br>
├── backend.tf<br>
├── providers.tf<br>
├── variables.tf<br>
├── outputs.tf<br>
└── main.tf<br>


---

## 🔧 Prerequisites

- Ubuntu 20.04 / 22.04+
- Git
- AWS CLI (if deploying to AWS)
- Terraform v1.x

---

### 1️⃣ Add HashiCorp GPG Key

```bash
wget -O - https://apt.releases.hashicorp.com/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gp
```
<br>

### 2️⃣ Add Official Repository
```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```
<br>

### 3️⃣ Install Terraform

```bash
sudo apt update
sudo apt install terraform -y
```
<br>

### 4️⃣ Verify Installation

```bash
terraform -version
```
<br>

### Install AWS CLI on Ubuntu
Download the aws cli bundle using below command

```bash
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
### Configure AWS CLI
To connect AWS using CLI we have configure AWS user using below command
```bash
aws configure
```

### 🚀 Usage

Initialize Terraform
```bash
terraform init
```

Validate Configuration
```bash
terraform validate
```

Plan Infrastructure Changes
```bash
terraform plan
```

Apply Changes
```bash
terraform apply
```

Destroy Infrastructure
```bash
terraform destroy
```
---
 ## Enlist the Blocks used in Terraform Language
 
### Terraform has multiple blocks (building units):

### provider → Defines the provider (AWS, Azure, etc.)
```bash
provider "aws" {
  region = "us-east-1"
}
```

### resource → Defines infrastructure resources.
```bash
resource "aws_instance" "example" {
  ami           = "ami-12345"
  instance_type = "t2.micro"
}
```

### variable → Input values (like parameters).
```bash
variable "region" {
  default = "us-east-1"
}
```

### output → Shows values after deployment.
```bash
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```
### module → Group of Terraform files reused as a package.
```bash
module "vpc" {
  source = "./modules/vpc"
}
```
### locals → Define local variables.
```bash
locals {
  env = "dev"
}
```

### data → Fetch existing info (e.g., latest AMI).
```bash
data "aws_ami" "latest" {
  most_recent = true
  owners      = ["amazon"]
}
```
---
### Terraform file that creates an EC2 instance on AWS
```bash
# Define the AWS provider
provider "aws" {
  region = "us-east-1"   
}

# Create an EC2 instance
resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0" 
  instance_type = "t2.micro"              

  tags = {
    Name = "MyFirstEC2"
  }
}


```
---

### Terraform file that creates a security group on AWS
```bash
provider "aws" {
  region = "us-east-1"
}

resource "aws_security_group" "web_sg" {
  name_prefix = "web-sg-"
  description = "Allow inbound HTTP and SSH traffic"

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
    Name = "web-sg"
  }
}

```
---
 ### HEREDOC in UserData
What is HEREDOC?
HEREDOC (Here Document) is a multi-line string syntax in Terraform used to define large blocks of text or commands. It is often utilized in UserData to pass startup scripts to cloud instances.

Example with UserData
Below is an example of using HEREDOC within an EC2 instance resource

```bash
resource "aws_instance" "web_server" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  user_data = <<-EOF
    #!/bin/bash
    yum update -y
    yum install -y httpd
    echo "Hello, World!" > /var/www/html/index.html
    systemctl start httpd
    systemctl enable httpd
  EOF

  tags = {
    Name = "web-server"
  }
}

```
### Another Example:

```bash
user_data = <<EOF
  ${file("index.sh")}
  EOF

```
### HEREDOC Syntax
<<-EOF: Begins the HEREDOC. 
-The - allows indentation.
-Content: The script or text.
-EOF: Ends the HEREDOC block.
---

### Key Blocks in the Terraform Script

Provider Block
The **`provider`** block specifies the cloud provider to manage resources.

Example:
```bash
provider "aws" {
  region = "us-east-1"
}
```

**`region`**: Defines the AWS region for resource deployment.

### Resource Block
The **`resource block`** defines the actual infrastructure components.

Example:
```bash
resource "aws_security_group" "web_sg" {
  name_prefix = "web-sg-"
  description = "Allow inbound HTTP and SSH traffic"
}
```
**`name_prefix`**: Prefix for the Security Group name.<br>
**`ingress`**/**`egress`**: Rules for inbound and outbound traffic.

### Variable Block
The **`variable`** block is used to parameterize values, making the script reusable.

Example:
```bash
variable "region" {
  default = "us-east-1"
}
```
-**`default`**: Specifies a default value.

### Data Block
The data block retrieves existing resources.

Example:
```bash
data "aws_ami" "latest" {
  most_recent = true
  owners      = ["self"]
}
```
most_recent: Fetches the latest AMI.

### Output Block
The output block displays resource attributes after execution.

Example:
```bash
output "security_group_id" {
  value = aws_security_group.web_sg.id
}
```
-**`value`**: Specifies the attribute to output.

 Applying the Script

### Steps:
1. Initialize Terraform:
   ```bash
   terraform init
   ```

2. Validate the script:
   ```bash
   terraform validate
   ```

3. Plan the execution:
   ```bash
   terraform plan
   ```

4. Apply the changes:
   ```bash
   terraform apply
   ```

5. Verify the Security Group in the AWS Console.
---
## Security group + Heredoc Hands-on .tf file
```hcl
provider "aws" {
  region = "ap-south-1"
}

variable "instance_type" {
  default = "t3.micro"
}
variable "ami_id" {
  default = "ami-019715e0d74f695be"
}

resource "aws_instance" "my-ec2" {
  ami = var.ami_id
  instance_type = var.instance_type
  security_groups = [aws_security_group.security.name]
  user_data = base64encode(<<-EOF 
      #!/bin/bash
      sudo apt update -y
      sudo apt install nginx -y 
      echo "Hello World from Terraform" > /var/www/html/index.html
      systemctl start nginx
      systemctl enable nginx
  EOF
  )
tags = {
    Name = "MyEC2Instance"
  }
}
resource "aws_security_group" "security" {
  name = "my-sg"
  description = "Allow SSH and HTTP traffic"

  ingress {
    from_port = 22
    to_port = 22
    protocol = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port = 80
    to_port = 80  
    protocol = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
  }
}


output "public_ip" {
  value = aws_instance.my-ec2.public_ip
}
```

