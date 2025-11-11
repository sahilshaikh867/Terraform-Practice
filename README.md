# 🚀 Terraform AWS Setup using GitHub Codespaces + VS Code

A fully working Terraform setup running inside **GitHub Codespaces**, using **secure Secrets**, a clean folder structure, and a demo **EC2 deployment**.

This guide is written for real beginners & DevOps learners who want practical hands-on Infra-as-Code experience.

---

## ✅ What we will do
✔ Create GitHub Repo  
✔ Launch Codespace  
✔ Configure AWS Credentials securely  
✔ Write Terraform code (EC2 + Security Group + Apache)  
✔ Deploy → Test → Destroy  
✔ GitHub-friendly doc with screenshot placeholders  

---

# 🧩 Step 1 — Create a GitHub Repository

✅ Create new repo → `terraform-infra`  
✅ Add a README.md  
✅ Enable Codespaces

📷 **Screenshot Placeholder:**  
[Repo Created](Images/repo.png)

---

# 🧩 Step 2 — Launch Codespaces

- Go to repo → **Code** → **Create Codespace on main**
- Browser-based VS Code will open.

📷 **Screenshot Placeholder:**  
[Codespace Launch](Images/codespace.png)

---

# 🧩 Step 3 — Add AWS Secrets (Never store keys in code)

1. Repo → Settings  
2. Secrets & Variables → Codespaces  
3. Add:

| Secret Name | Value |
|-------------|-------|
| `AWS_ACCESS_KEY_ID` | Your Key |
| `AWS_SECRET_ACCESS_KEY` | Your Secret |
| `AWS_DEFAULT_REGION` | ap-south-1 |

📷 Placeholder:  
[Secrets Setup](Images/codespace-secrets.png)

✅ Terraform will now read credentials securely from environment variables.

---

# 🧩 Step 4 — Install VS Code Extensions

✔ HashiCorp Terraform  
✔ AWS Toolkit  
✔ YAML  
✔ Prettier  

---

# 📁 Step 5 — Project Folder Structure

```

terraform-infra/
├─ envs/
│  ├─ dev.tfvars
├─ main.tf
├─ providers.tf
├─ variables.tf
├─ outputs.tf
├─ versions.tf
├─ terraform.tfvars (optional)
├─ .gitignore
└─ README.md

```

# ✅ Step 6 — Create `.gitignore`

```

.terraform/
.terraform.lock.hcl
terraform.tfstate
terraform.tfstate.backup
*.tfvars
crash.log

````
---

# ✅ Step 7 — Terraform Code

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

### ✅ `providers.tf`

```hcl
provider "aws" {
  region = var.aws_region
}
```

### ✅ `variables.tf`

```hcl
variable "aws_region" {
  type    = string
  default = "ap-south-1"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "ec2_name" {
  default = "tf-demo-ec2"
}

variable "key_name" {
  default = "id_rsa"
}
```

### ✅ `main.tf`

```hcl
data "aws_vpc" "default" {
  default = true
}

data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-noble-24.04-amd64-server-*"]
  }
}

resource "aws_security_group" "basic_sg" {
  name        = "tf-basic-sg"
  description = "Allow SSH & HTTP"
  vpc_id      = data.aws_vpc.default.id

  ingress {
    from_port = 22
    to_port   = 22
    protocol  = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port = 80
    to_port   = 80
    protocol  = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port = 0
    to_port   = 0
    protocol  = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "this" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = var.instance_type
  vpc_security_group_ids = [aws_security_group.basic_sg.id]
  associate_public_ip_address = true
  key_name = var.key_name

  user_data = <<-EOT
    #!/bin/bash
    apt-get update -y
    apt-get install -y apache2
    echo "Welcome from Terraform on $(hostname)" > /var/www/html/index.html
    systemctl enable --now apache2
  EOT

  tags = {
    Name = var.ec2_name
  }
}
```

### ✅ `outputs.tf`

```hcl
output "public_ip" {
  value = aws_instance.this.public_ip
}

output "public_dns" {
  value = aws_instance.this.public_dns
}
```

### ✅ `envs/dev.tfvars`

```
aws_region   = "ap-south-1"
instance_type = "t2.micro"
ec2_name     = "tf-dev-ec2"
key_name     = "id_rsa"
```

---

# 🔥 Step 8 — Terraform Commands

### ✅ Initialize

```bash
terraform init
```
---

### ✅ Format + Validate

```bash
terraform fmt -recursive
terraform validate
```
---

### ✅ Create Plan

```bash
terraform plan -var-file=envs/dev.tfvars
```
---

### ✅ Apply (Deploy EC2)

```bash
terraform apply -var-file=envs/dev.tfvars
```

→ Type **yes**

---

# ✅ Step 9 — Test Deployment

```bash
terraform output
```

Open browser:

```
http://<your-public-ip>
```

✅ You should see: `"Welcome from Terraform"`

📷 Placeholder:
`![EC2 Running](images/ec2-web.png)`

---

# ✅ Step 10 — Destroy (Cleanup)

```bash
terraform destroy -var-file=envs/dev.tfvars
```

📷 Placeholder:
`![TF Destroy](images/tf-destroy.png)`

---

# ✅ ✅ Bonus — Key Best Practices

✅ Never commit secrets
✅ Use tfvars for env configs
✅ Use .gitignore always
✅ Future upgrade: S3 remote backend + DynamoDB locking

---

# ✅ Author

**Sahil — DevOps Learner (SPPU), Cloud + Automation ♥**
Follow for more Infra-as-Code labs!

---

# ✨ Final Note

✅ Beginner-friendly
✅ Works inside browser
✅ Professional GitHub-readable documentation
✅ Ready for interview-level demonstration


