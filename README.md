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
2️⃣ Add Official Repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
3️⃣ Install Terraform
sudo apt update
sudo apt install terraform -y
4️⃣ Verify Installation
terraform -version
🚀 Usage
Initialize Terraform
terraform init
Validate Configuration
terraform validate
Plan Infrastructure Changes
terraform plan
Apply Changes
terraform apply
Destroy Infrastructure
terraform destroy

## ⚙️ Install Terraform (Ubuntu)

### 1️⃣ Add HashiCorp GPG Key

```bash
wget -O - https://apt.releases.hashicorp.com/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
