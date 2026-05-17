# 🌐 terraform-aws-vpc

<div align="center">

![Terraform](https://img.shields.io/badge/Terraform-1.x-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![VPC](https://img.shields.io/badge/AWS-VPC-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![IaC](https://img.shields.io/badge/IaC-Infrastructure_as_Code-0052CC?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-00C853?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

**Production-ready AWS VPC infrastructure provisioned with Terraform — clean, modular, and beginner-friendly.**

[🚀 Quick Start](#-quick-start) · [📐 Architecture](#-architecture) · [⚙️ Features](#️-features) · [📖 Documentation](#-terraform-workflow) · [👨‍💻 Author](#-author)

</div>

---

## 📌 Project Overview

**terraform-aws-vpc** is a real-world Infrastructure as Code (IaC) project that provisions a complete AWS Virtual Private Cloud (VPC) networking environment using Terraform. This project is designed to help DevOps engineers, cloud practitioners, and students understand the fundamentals of AWS networking and how to automate cloud infrastructure using Terraform.

Whether you're preparing for an AWS certification, building your DevOps portfolio, or exploring cloud networking concepts — this project is your hands-on starting point.

> 💡 **Why this project?** Manual cloud infrastructure is error-prone, slow, and hard to replicate. Terraform eliminates that complexity by letting you define infrastructure declaratively and deploy it consistently across environments.

---

## 📐 Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                          AWS CLOUD                                  │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                      VPC (10.0.0.0/16)                       │  │
│  │                                                               │  │
│  │   ┌─────────────────────┐    ┌─────────────────────────────┐ │  │
│  │   │   Public Subnet     │    │      Private Subnet         │ │  │
│  │   │   (10.0.1.0/24)     │    │      (10.0.2.0/24)          │ │  │
│  │   │                     │    │                             │ │  │
│  │   │  ┌──────────────┐   │    │  ┌──────────────────────┐  │ │  │
│  │   │  │ EC2 / Load   │   │    │  │  Databases / Private │  │ │  │
│  │   │  │  Balancer    │   │    │  │     App Servers      │  │ │  │
│  │   │  └──────────────┘   │    │  └──────────────────────┘  │ │  │
│  │   └──────────┬──────────┘    └─────────────────────────────┘ │  │
│  │              │                                                │  │
│  │   ┌──────────▼──────────┐                                    │  │
│  │   │    Route Table      │                                    │  │
│  │   │  0.0.0.0/0 → IGW    │                                    │  │
│  │   └──────────┬──────────┘                                    │  │
│  │              │                                                │  │
│  └──────────────┼────────────────────────────────────────────── ┘  │
│                 │                                                   │
│  ┌──────────────▼──────────┐                                        │
│  │   Internet Gateway (IGW)│                                        │
│  └──────────────┬──────────┘                                        │
│                 │                                                   │
└─────────────────┼───────────────────────────────────────────────────┘
                  │
              🌍 Internet
```

---

## ⚙️ Features

| Feature | Description | Status |
|---|---|---|
| 🏗️ Custom VPC | Isolated virtual network with custom CIDR block | ✅ Done |
| 🌍 Public Subnet | Internet-accessible subnet for web-facing resources | ✅ Done |
| 🔒 Private Subnet | Internal subnet for secure backend services | ✅ Done |
| 🔁 Internet Gateway | Connects VPC to the public internet | ✅ Done |
| 🛤️ Route Table | Custom routing rules for traffic management | ✅ Done |
| 🔗 Route Table Association | Links subnets to their respective route tables | ✅ Done |
| 📦 Infrastructure as Code | Fully automated, version-controlled infrastructure | ✅ Done |
| ☁️ Terraform AWS Provider | Uses official HashiCorp AWS provider | ✅ Done |

---

## 🛠️ Technologies Used

| Technology | Purpose | Version |
|---|---|---|
| ![Terraform](https://img.shields.io/badge/-Terraform-7B42BC?logo=terraform&logoColor=white) | Infrastructure provisioning | >= 1.0 |
| ![AWS](https://img.shields.io/badge/-Amazon_AWS-FF9900?logo=amazonaws&logoColor=white) | Cloud provider | Latest |
| ![Linux](https://img.shields.io/badge/-Linux-FCC624?logo=linux&logoColor=black) | Operating system | Ubuntu 20.04+ |
| HashiCorp AWS Provider | Terraform AWS integration | ~> 5.0 |

---

## 📋 Prerequisites

Before you begin, make sure you have the following installed and configured:

- ✅ [Terraform](https://developer.hashicorp.com/terraform/downloads) `>= 1.0`
- ✅ [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) configured with credentials
- ✅ An active [AWS Account](https://aws.amazon.com/free/)
- ✅ Basic understanding of AWS VPC concepts
- ✅ Linux/macOS terminal or WSL on Windows

### AWS CLI Configuration

```bash
aws configure
# AWS Access Key ID: <your-access-key>
# AWS Secret Access Key: <your-secret-key>
# Default region name: us-east-1
# Default output format: json
```

---

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/CodebyHarris/terraform-aws-vpc.git
cd terraform-aws-vpc
```

### 2. Review and Customize Variables

Open `variables.tf` and adjust the values as needed:

```hcl
variable "vpc_cidr" {
  default = "10.0.0.0/16"
}

variable "public_subnet_cidr" {
  default = "10.0.1.0/24"
}

variable "private_subnet_cidr" {
  default = "10.0.2.0/24"
}

variable "region" {
  default = "us-east-1"
}
```

---

## 📖 Terraform Workflow

Follow this four-step Terraform workflow to deploy your infrastructure:

### Step 1 — Initialize Terraform

Downloads and sets up the required AWS provider plugins.

```bash
terraform init
```

**Expected Output:**
```
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 5.0"...
- Installing hashicorp/aws v5.x.x...
✅ Terraform has been successfully initialized!
```

---

### Step 2 — Validate Configuration

Checks for syntax errors and validates the configuration files.

```bash
terraform validate
```

**Expected Output:**
```
✅ Success! The configuration is valid.
```

---

### Step 3 — Preview the Plan

Shows a detailed execution plan of all resources that will be created, modified, or destroyed — without making any changes.

```bash
terraform plan
```

**Expected Output:**
```
Plan: 6 to add, 0 to change, 0 to destroy.

  + aws_vpc.main
  + aws_subnet.public
  + aws_subnet.private
  + aws_internet_gateway.igw
  + aws_route_table.public_rt
  + aws_route_table_association.public_assoc
```

---

### Step 4 — Apply the Infrastructure

Provisions all AWS resources defined in your Terraform configuration.

```bash
terraform apply
```

Type `yes` when prompted to confirm. 

**Expected Output:**
```
Apply complete! Resources: 6 added, 0 changed, 0 destroyed.
```

---

### 🗑️ Destroy Infrastructure (Cleanup)

When you're done, tear down all provisioned resources to avoid unnecessary AWS charges:

```bash
terraform destroy
```

---

## 📁 File Structure

```
terraform-aws-vpc/
│
├── 📄 main.tf               # Core infrastructure resources (VPC, Subnets, IGW, Routes)
├── 📄 variables.tf          # Input variable declarations (CIDR blocks, region, tags)
├── 📄 outputs.tf            # Output values (VPC ID, Subnet IDs, etc.)
├── 📄 provider.tf           # AWS provider configuration and region settings
├── 📄 terraform.tfvars      # Variable values (excluded from Git for security)
│
├── 📄 .gitignore            # Ignores .terraform/, *.tfstate, *.tfvars
├── 📄 README.md             # Project documentation (you are here!)
│
└── 📁 .terraform/           # Auto-generated provider plugins (gitignored)
```

---

## 🌐 Networking Concepts Explained

Understanding AWS networking is key to working with this project. Here's a breakdown of every resource provisioned:

### 🏠 VPC — Virtual Private Cloud

| Property | Value |
|---|---|
| CIDR Block | `10.0.0.0/16` |
| Total IPs | 65,536 |
| Purpose | Isolated virtual network within AWS |

> A VPC is your own private section of the AWS cloud. Think of it as your private data center in the cloud where you control all networking.

---

### 🌍 Public Subnet

| Property | Value |
|---|---|
| CIDR Block | `10.0.1.0/24` |
| Total IPs | 256 |
| Internet Access | ✅ Yes (via IGW) |
| Use Case | Web servers, load balancers, bastion hosts |

---

### 🔒 Private Subnet

| Property | Value |
|---|---|
| CIDR Block | `10.0.2.0/24` |
| Total IPs | 256 |
| Internet Access | ❌ No (requires NAT Gateway) |
| Use Case | Databases, application servers, internal services |

---

### 🔁 Internet Gateway (IGW)

An Internet Gateway allows resources in your VPC to communicate with the internet. It is attached to the VPC and referenced in the route table.

```hcl
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id
}
```

---

### 🛤️ Route Table

The route table defines how traffic is directed within the VPC. The public route table sends all outbound traffic (`0.0.0.0/0`) to the Internet Gateway.

```hcl
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}
```

---

## 🎓 Learning Outcomes

By completing this project, you will:

- 🧠 Understand how AWS VPC networking works at a foundational level
- 🏗️ Know how to write and structure Terraform configuration files
- 🔄 Be familiar with the complete Terraform workflow: `init → validate → plan → apply → destroy`
- 🌐 Differentiate between public and private subnets and their use cases
- 🛤️ Understand how Route Tables and Internet Gateways connect VPCs to the internet
- 📦 Apply Infrastructure as Code principles to real cloud environments
- 🔧 Debug and troubleshoot Terraform deployments

---

## 🗺️ Future Improvements

This project is a solid foundation. Here's the roadmap for upcoming enhancements:

| Feature | Description | Priority |
|---|---|---|
| 🔄 NAT Gateway | Allow private subnet resources to access the internet securely | 🔴 High |
| 🖥️ EC2 Deployment | Launch EC2 instances inside public and private subnets | 🔴 High |
| 🔐 Security Groups | Define inbound/outbound firewall rules for resources | 🟡 Medium |
| 🌍 Multi-AZ Setup | Deploy subnets across multiple Availability Zones for high availability | 🟡 Medium |
| 🔀 Load Balancer | Add Application Load Balancer for traffic distribution | 🟢 Planned |
| 📊 VPC Flow Logs | Enable traffic monitoring and logging for security audits | 🟢 Planned |
| 🧩 Terraform Modules | Refactor into reusable, shareable Terraform modules | 🟢 Planned |

---

## 🏷️ Recommended GitHub Topics

Add these topics to your repository to improve discoverability:

```
terraform  aws  vpc  devops  infrastructure-as-code  cloud-computing
aws-vpc  terraform-aws  networking  iac  cloud-infrastructure  hashicorp
aws-networking  public-subnet  private-subnet  internet-gateway  beginner-friendly
```

---

## 👨‍💻 Author

<div align="center">

### **Muhammad Haris**
#### `CodebyHarris`

*DevOps Engineer | Cloud Practitioner | IaC Enthusiast*

[![GitHub](https://img.shields.io/badge/GitHub-CodebyHarris-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/CodebyHarris)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Muhammad_Haris-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/CodebyHarris)

</div>

---

## 📄 License

This project is licensed under the **MIT License** — feel free to use, modify, and share it.

See the [LICENSE](LICENSE) file for full details.

---

<div align="center">

**⭐ If this project helped you, please give it a star — it helps others find it too!**

---

*Built with ❤️ by [CodebyHarris](https://github.com/CodebyHarris) — Turning cloud concepts into real infrastructure, one `terraform apply` at a time.*

![Visitors](https://img.shields.io/badge/DevOps-Portfolio_Project-FF6B35?style=flat-square)
![Made with Terraform](https://img.shields.io/badge/Made_with-Terraform-7B42BC?style=flat-square&logo=terraform)
![Powered by AWS](https://img.shields.io/badge/Powered_by-AWS-FF9900?style=flat-square&logo=amazonaws)

</div>
