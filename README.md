# 🌐 terraform-aws-vpc

<div align="center">

![Terraform](https://img.shields.io/badge/Terraform-1.x-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Region](https://img.shields.io/badge/Region-us--east--2-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![IaC](https://img.shields.io/badge/IaC-Infrastructure_as_Code-0052CC?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-00C853?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

**Production-ready AWS VPC networking infrastructure for an e-commerce platform — provisioned with Terraform in a single clean file.**

[🚀 Quick Start](#-quick-start) · [📐 Architecture](#-architecture) · [⚙️ Resources](#️-resources-provisioned) · [📖 Workflow](#-terraform-workflow) · [👨‍💻 Author](#-author)

</div>

---

## 📌 Project Overview

**terraform-aws-vpc** provisions a complete AWS Virtual Private Cloud (VPC) networking environment tailored for an **e-commerce application**. The entire infrastructure is defined in a single Terraform file — `aws_vpc.tf` — making it clean, readable, and easy to understand for beginners while being practical enough for real-world use.

The project creates an isolated network with a **public subnet** for internet-facing resources and a **private subnet** for backend services, connected through an **Internet Gateway** and managed via a **Route Table**.

> 💡 **Region:** All resources are deployed in `us-east-2` (Ohio).

---

## 📐 Architecture

```
                          🌍 Internet
                               │
               ┌───────────────▼────────────────┐
               │     Internet Gateway (IGW)      │
               │        ecommerce-igw            │
               └───────────────┬────────────────┘
                               │
               ┌───────────────▼────────────────┐
               │          Route Table            │
               │       ecommerce-public-rtb      │
               │       0.0.0.0/0  →  IGW         │
               └───────────────┬────────────────┘
                               │  (Route Table Association)
┌──────────────────────────────▼──────────────────────────────────────┐
│                      VPC  —  ecommerce-vpc                          │
│                         CIDR: 10.0.0.0/16                           │
│                                                                      │
│   ┌───────────────────────────┐   ┌──────────────────────────────┐  │
│   │      Public Subnet        │   │       Private Subnet          │  │
│   │  ecommerce-public-subnet  │   │  ecommerce-private-subnet     │  │
│   │      10.0.1.0/24          │   │       10.0.2.0/24             │  │
│   │  map_public_ip = true ✅  │   │   map_public_ip = false ❌    │  │
│   │                           │   │                               │  │
│   │  Web Servers / LB /       │   │  Databases / App Servers /    │  │
│   │  Bastion Hosts            │   │  Internal Services            │  │
│   └───────────────────────────┘   └──────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

---

## ⚙️ Resources Provisioned

All **6 resources** are defined inside the single file `aws_vpc.tf`:

| # | Resource Type | Terraform Name | AWS Name Tag |
|---|---|---|---|
| 1 | `aws_vpc` | `main` | `ecommerce-vpc` |
| 2 | `aws_subnet` (public) | `public_subnet` | `ecommerce-public-subnet` |
| 3 | `aws_subnet` (private) | `private_subnet` | `ecommerce-private-subnet` |
| 4 | `aws_internet_gateway` | `gw` | `ecommerce-igw` |
| 5 | `aws_route_table` | `ecommerce_rtb` | `ecommerce-public-rtb` |
| 6 | `aws_route_table_association` | `rtb_association` | *(no tag needed)* |

---

## 📄 The Terraform File — `aws_vpc.tf`

```hcl
provider "aws" {
  region = "us-east-2"
}

resource "aws_vpc" "main" {
  cidr_block       = "10.0.0.0/16"
  instance_tenancy = "default"
  tags = {
    Name = "ecommerce-vpc"
  }
}

resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true
  tags = {
    Name = "ecommerce-public-subnet"
  }
}

resource "aws_subnet" "private_subnet" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.2.0/24"
  tags = {
    Name = "ecommerce-private-subnet"
  }
}

resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main.id
  tags = {
    Name = "ecommerce-igw"
  }
}

resource "aws_route_table" "ecommerce_rtb" {
  vpc_id = aws_vpc.main.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }
  tags = {
    Name = "ecommerce-public-rtb"
  }
}

resource "aws_route_table_association" "rtb_association" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.ecommerce_rtb.id
}
```

---

## 🌐 Networking Deep Dive

### 🏠 VPC — `ecommerce-vpc`

| Property | Value |
|---|---|
| CIDR Block | `10.0.0.0/16` |
| Total IP Addresses | 65,536 |
| Tenancy | `default` (shared hardware) |
| Region | `us-east-2` (Ohio) |

The VPC is the **root of all networking** in this project. Every other resource — subnets, gateways, route tables — lives inside it. The `/16` CIDR gives plenty of room to carve out multiple subnets as the project grows.

---

### 🌍 Public Subnet — `ecommerce-public-subnet`

| Property | Value |
|---|---|
| CIDR Block | `10.0.1.0/24` |
| Usable IPs | 251 (AWS reserves 5 per subnet) |
| Auto-Assign Public IP | ✅ `true` |
| Internet Access | ✅ Yes, via IGW + Route Table |

`map_public_ip_on_launch = true` means any EC2 instance launched here **automatically gets a public IP** — no manual assignment needed. This is the right subnet for web servers, load balancers, and bastion hosts.

---

### 🔒 Private Subnet — `ecommerce-private-subnet`

| Property | Value |
|---|---|
| CIDR Block | `10.0.2.0/24` |
| Usable IPs | 251 (AWS reserves 5 per subnet) |
| Auto-Assign Public IP | ❌ `false` (default) |
| Internet Access | ❌ No (NAT Gateway needed for outbound) |

Resources here are **not reachable from the internet** — ideal for databases (RDS), internal APIs, and application servers that should never be publicly exposed.

---

### 🔁 Internet Gateway — `ecommerce-igw`

The IGW is attached directly to `ecommerce-vpc` and acts as the **bridge between the VPC and the public internet**. Without it, no traffic can flow in or out — even from the public subnet.

---

### 🛤️ Route Table — `ecommerce-public-rtb`

| Destination | Target | Purpose |
|---|---|---|
| `10.0.0.0/16` | `local` | Internal VPC traffic (auto-added by AWS) |
| `0.0.0.0/0` | `ecommerce-igw` | All outbound internet traffic |

The route `0.0.0.0/0 → IGW` tells AWS: *"send any traffic not destined for the local VPC out through the Internet Gateway."*

---

### 🔗 Route Table Association — `rtb_association`

This resource **links** `ecommerce-public-subnet` to `ecommerce-public-rtb`. Without this association, the subnet falls back to the VPC's default route table and loses internet access.

> ⚠️ **Note:** The private subnet intentionally has **no route table association** — it uses the VPC's default route table, which has no internet route. This is by design.

---

## 📁 File Structure

```
terraform-aws-vpc/
│
├── 📄 aws_vpc.tf      # All infrastructure: provider, VPC, subnets, IGW, route table
└── 📄 README.md       # Project documentation (you are here!)
```

> This project uses a **single-file structure** intentionally — everything lives in `aws_vpc.tf` for simplicity and learning. As projects grow, resources are typically split into `main.tf`, `variables.tf`, `outputs.tf`, and `provider.tf`.

---

## 🛠️ Prerequisites

Before running this project, ensure you have:

- ✅ [Terraform](https://developer.hashicorp.com/terraform/downloads) `>= 1.0` installed
- ✅ [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) installed and configured
- ✅ An active [AWS Account](https://aws.amazon.com/free/)
- ✅ IAM user with `AmazonVPCFullAccess` permissions (or Admin for learning)

### Configure AWS CLI

```bash
aws configure
# AWS Access Key ID:     <your-access-key>
# AWS Secret Access Key: <your-secret-key>
# Default region name:   us-east-2
# Default output format: json
```

---

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/CodebyHarris/terraform-aws-vpc.git
cd terraform-aws-vpc
```

---

## 📖 Terraform Workflow

### ① Initialize — Download AWS Provider

```bash
terraform init
```

Downloads the **HashiCorp AWS provider** plugin required to communicate with AWS APIs.

```
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/aws versions...
- Installing hashicorp/aws v5.x.x...

✅ Terraform has been successfully initialized!
```

---

### ② Validate — Check for Errors

```bash
terraform validate
```

Checks `aws_vpc.tf` for syntax errors and configuration issues **before** touching any AWS resources.

```
✅ Success! The configuration is valid.
```

---

### ③ Plan — Preview What Will Be Created

```bash
terraform plan
```

Shows exactly what Terraform will do — safely, without making any changes to AWS.

```
Plan: 6 to add, 0 to change, 0 to destroy.

  + aws_vpc.main
  + aws_subnet.public_subnet
  + aws_subnet.private_subnet
  + aws_internet_gateway.gw
  + aws_route_table.ecommerce_rtb
  + aws_route_table_association.rtb_association
```

---

### ④ Apply — Deploy to AWS

```bash
terraform apply
```

Type `yes` when prompted. Terraform creates all 6 resources in `us-east-2`.

```
aws_vpc.main: Creating...
aws_vpc.main: Creation complete [id=vpc-xxxxxxxxxxxxxxxxx]
aws_subnet.public_subnet: Creating...
aws_subnet.private_subnet: Creating...
aws_internet_gateway.gw: Creating...
aws_route_table.ecommerce_rtb: Creating...
aws_route_table_association.rtb_association: Creating...

✅ Apply complete! Resources: 6 added, 0 changed, 0 destroyed.
```

---

### ⑤ Destroy — Clean Up (Avoid AWS Charges)

```bash
terraform destroy
```

```
✅ Destroy complete! Resources: 6 destroyed.
```

> 💰 **Important:** Always run `terraform destroy` when done experimenting to avoid unexpected AWS charges.

---

## 🎓 Learning Outcomes

By studying and running this project, you will:

- 🧠 Understand how AWS VPC components connect to form a complete network
- 🌐 Know the difference between public and private subnets and when to use each
- 🔁 Understand how Internet Gateways and Route Tables enable internet connectivity
- 📦 Write real Terraform HCL code with providers, resources, and tags
- 🔄 Execute the full Terraform lifecycle: `init → validate → plan → apply → destroy`
- 🏷️ Apply AWS resource tagging best practices for organized cloud infrastructure
- 🏗️ Understand single-file vs. multi-file Terraform project structures

---

## 🗺️ Future Improvements

| Feature | Description | Priority |
|---|---|---|
| 🔄 NAT Gateway | Allow private subnet to reach the internet securely (outbound only) | 🔴 High |
| 🖥️ EC2 Instances | Deploy web servers in public subnet, app servers in private | 🔴 High |
| 🔐 Security Groups | Firewall rules to control inbound/outbound traffic per resource | 🟡 Medium |
| 🌍 Multi-AZ Setup | Replicate subnets across Availability Zones for high availability | 🟡 Medium |
| 📊 VPC Flow Logs | Enable network traffic monitoring via CloudWatch | 🟢 Planned |
| 🧩 Terraform Modules | Refactor into reusable modules for multi-environment deployments | 🟢 Planned |
| 📁 Split File Structure | Separate into `main.tf`, `variables.tf`, `outputs.tf`, `provider.tf` | 🟢 Planned |

---

## 🏷️ Recommended GitHub Topics

Add these to your repository settings under **Topics** for better discoverability:

```
terraform  aws  vpc  devops  infrastructure-as-code  cloud-computing
aws-vpc  terraform-aws  networking  iac  ecommerce-infrastructure
us-east-2  public-subnet  private-subnet  internet-gateway  route-table
hashicorp  beginner-friendly  portfolio-project
```

---

## 👨‍💻 Author

<div align="center">

### **Muhammad Haris**
#### `CodebyHarris`

*DevOps Engineer · Cloud Practitioner · IaC Enthusiast*

[![GitHub](https://img.shields.io/badge/GitHub-CodebyHarris-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/CodebyHarris)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Muhammad_Haris-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/CodebyHarris)

</div>

---

## 📄 License

This project is licensed under the **MIT License** — free to use, modify, and share.

---

<div align="center">

**⭐ Found this helpful? Drop a star — it helps other learners find this project!**

---

*Built with ❤️ by [CodebyHarris](https://github.com/CodebyHarris)*
*— Turning cloud concepts into real infrastructure, one `terraform apply` at a time.*

![Made with Terraform](https://img.shields.io/badge/Made_with-Terraform-7B42BC?style=flat-square&logo=terraform)
![Powered by AWS](https://img.shields.io/badge/Powered_by-AWS-FF9900?style=flat-square&logo=amazonaws)
![Region](https://img.shields.io/badge/Region-us--east--2-232F3E?style=flat-square&logo=amazonaws)

</div>
