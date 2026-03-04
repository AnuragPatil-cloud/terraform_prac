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
---
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
```bash
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
  ami           = var.ami_id
  instance_type = var.instance_type
  security_groups = [aws_security_group.security.name]

  user_data = base64encode(<<-EOF
#!/bin/bash
sudo apt update -y
sudo apt install nginx -y
echo "Hello World from Terraform" > /var/www/html/index.html
sudo systemctl start nginx
sudo systemctl enable nginx
EOF
  )

  tags = {
    Name = "MyEC2Instance"
  }
}

resource "aws_security_group" "security" {
  name        = "my-sg"
  description = "Allow SSH and HTTP traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

output "public_ip" {
  value = aws_instance.my-ec2.public_ip
}

```
---
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
}

# ---------------- Internet Gateway ----------------
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main.id
}

# ---------------- Public Subnet 1 ----------------
resource "aws_subnet" "subnet1" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "ap-south-1a"
  map_public_ip_on_launch = true
}

# ---------------- Public Subnet 2 ----------------
resource "aws_subnet" "subnet2" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.2.0/24"
  availability_zone       = "ap-south-1b"
  map_public_ip_on_launch = true
}

# ---------------- Route Table ----------------
resource "aws_route_table" "rt" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
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
}

# ---------------- EC2 Instance ----------------
resource "aws_instance" "app" {
  ami                         = "ami-019715e0d74f695be"
  instance_type               = "t3.micro"
  subnet_id                   = aws_subnet.subnet1.id
  vpc_security_group_ids      = [aws_security_group.alb_sg.id]
  associate_public_ip_address = true

  user_data =  base64encode(<<-EOF
              #!/bin/bash
              yum install -y httpd
              systemctl start httpd
              systemctl enable httpd
              echo "Hello from Terraform ALB" > /var/www/html/index.html
              EOF
                 )
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

output "load_balancer_dns" {
  value = aws_lb.alb.dns_name
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
