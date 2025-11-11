# 🌐 Terraform AWS VPC Infrastructure (Public + Private Subnets)

This project builds a complete AWS network using Terraform:

✅ Custom VPC  
✅ Public & Private Subnets  
✅ Internet Gateway  
✅ NAT Gateway  
✅ Route Tables & Associations  
✅ Web Server in Public Subnet  
✅ App Server in Private Subnet (Internet via NAT)

This setup is ideal for real-world production architecture & interview demos.

---

## ✅ Architecture Diagram (Explain in README)

```
       Internet
           │
   ┌──────────────┐
   │ Internet GW  │
   └──────┬───────┘
          │
┌─────────▼─────────┐
│   Public Subnet   │  EC2 (Frontend / Apache)
└─────────┬─────────┘
          │
   ┌──────▼──────┐
   │ NAT Gateway │
   └──────┬──────┘
          │
┌─────────▼────────-─┐
│  Private Subnet    │  EC2 (Backend / DB)
└────────────────────┘
```

# ✅ Step 1 — Folder Structure

```

vpc-terraform/
├─ main.tf
├─ variables.tf
├─ outputs.tf
├─ providers.tf
├─ versions.tf
├─ envs/
│   ├─ dev.tfvars
├─ .gitignore
└─ README.md

````
# ✅ Step 2 — Terraform Files

### ✅ `versions.tf`
```hcl
terraform {
  required_version = ">= 1.6.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.60"
    }
  }
}
````

---

### ✅ `providers.tf`

```hcl
provider "aws" {
  region = var.aws_region
}
```

---

### ✅ `variables.tf`

```hcl
variable "aws_region" {
  default = "ap-south-1"
}

variable "vpc_cidr" {
  default = "192.168.0.0/16"
}

variable "public_subnet" {
  default = "192.168.1.0/24"
}

variable "private_subnet" {
  default = "192.168.2.0/24"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {
  default = "id_rsa"
}
```

---

### ✅ `main.tf`

```hcl
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "tf-main-vpc"
  }
}

# Public Subnet
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = var.public_subnet
  map_public_ip_on_launch = true
  availability_zone       = "ap-south-1a"

  tags = {
    Name = "public-subnet"
  }
}

# Private Subnet
resource "aws_subnet" "private" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.private_subnet
  availability_zone = "ap-south-1a"

  tags = {
    Name = "private-subnet"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "tf-igw"
  }
}

# Public Route Table
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "public-rt"
  }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public_rt.id
}

# Elastic IP for NAT
resource "aws_eip" "nat_ip" {
  vpc = true
}

# NAT Gateway
resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat_ip.id
  subnet_id     = aws_subnet.public.id

  depends_on = [aws_internet_gateway.igw]

  tags = {
    Name = "nat-gateway"
  }
}

# Private Route Table
resource "aws_route_table" "private_rt" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.nat.id
  }

  tags = {
    Name = "private-rt"
  }
}

resource "aws_route_table_association" "private_assoc" {
  subnet_id      = aws_subnet.private.id
  route_table_id = aws_route_table.private_rt.id
}

# Security Group
resource "aws_security_group" "web_sg" {
  name   = "public-sg"
  vpc_id = aws_vpc.main.id

  ingress {
    from_port = 80
    to_port   = 80
    protocol  = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port = 22
    to_port   = 22
    protocol  = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port = 0
    to_port   = 0
    protocol  = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "web-sg"
  }
}

# EC2 in Public Subnet
resource "aws_instance" "frontend" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.web_sg.id]
  associate_public_ip_address = true
  key_name = var.key_name

  user_data = <<-EOT
    #!/bin/bash
    apt update -y
    apt install -y apache2
    systemctl enable --now apache2
    echo "<h1>Hello from PUBLIC EC2</h1>" > /var/www/html/index.html
  EOT

  tags = {
    Name = "frontend-public"
  }
}

# EC2 in Private Subnet
resource "aws_instance" "backend" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.private.id
  key_name      = var.key_name

  tags = {
    Name = "backend-private"
  }
}

# Ubuntu AMI Data Source
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-noble-24.04-amd64-server-*"]
  }
}
```

---

### ✅ `outputs.tf`

```hcl
output "public_ec2_ip" {
  value = aws_instance.frontend.public_ip
}

output "private_ec2_ip" {
  value = aws_instance.backend.private_ip
}
```

---

### ✅ `envs/dev.tfvars`

```
aws_region   = "ap-south-1"
instance_type = "t2.micro"
key_name     = "id_rsa"
```

---

# ✅ Step 3 — Run Terraform

```bash
terraform init
terraform validate
terraform plan -var-file=envs/dev.tfvars
terraform apply -var-file=envs/dev.tfvars
```
# ✅ Step 4 — Test

✅ Copy Public EC2 IP → Browser

```
http://<public-ip>
```

You should see:

```
Hello from PUBLIC EC2
```

✅ Check Private EC2 can reach internet (SSH through public):

```bash
curl google.com
```
---

# ✅ Step 5 — Destroy Infra

```bash
terraform destroy -var-file=envs/dev.tfvars
```
-----------------------------------

# ✅ Author

**Sahil — DevOps & Cloud Learner | Terraform | AWS | Docker | Kubernetes**
Always Building. Always Automating. ⚙️🔥

---

# ✅ Extra (optional upgrades)

* Add Load Balancer
* Add RDS in private subnet
* Add S3 remote backend + DynamoDB state locking

---

```
✅ Full VPC Infra  
✅ Production-style architecture  
✅ Markdown ready  
✅ Screenshot placeholders  
✅ Deploy + test + destroy  
