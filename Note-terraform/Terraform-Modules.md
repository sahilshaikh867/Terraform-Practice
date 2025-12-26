🔥 **Terraform Modules – Real Project Style**

---

# 🧠 WHAT IS A TERRAFORM MODULE?

> A **module** is a reusable collection of Terraform code.

Simple words:

* Folder = module
* `.tf` files inside = logic
* Variables = inputs
* Outputs = results

Terraform project khud bhi **root module** hota hai.

---

# ❓ WHY MODULES EXIST (INTERVIEW ANSWER)

Without modules:

* Duplicate code
* Hard to manage
* Error-prone

With modules:

* Reusable
* Clean
* Scalable
* Team-friendly

👉 **Industry standard Terraform uses modules.**

---

# 🧱 REAL PROJECT STRUCTURE (IMPORTANT)

```text
terraform-project/
├── modules/
│   └── ec2/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfvars
```

This structure alone impresses interviewers 👀

---

# 📦 MODULE: EC2 (Reusable)

## 📄 modules/ec2/variables.tf

```hcl
variable "instance_type" {
  type = string
}

variable "ami_id" {
  type = string
}
```

---

## 📄 modules/ec2/main.tf

```hcl
resource "aws_instance" "this" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

---

## 📄 modules/ec2/outputs.tf

```hcl
output "instance_id" {
  value = aws_instance.this.id
}
```

---

# 🧠 ROOT MODULE (MAIN PROJECT)

## 📄 main.tf

```hcl
provider "aws" {
  region = "ap-south-1"
}

module "web_server" {
  source        = "./modules/ec2"
  ami_id        = "ami-0abcdef123"
  instance_type = "t2.micro"
}
```

---

## 📄 outputs.tf

```hcl
output "web_instance_id" {
  value = module.web_server.instance_id
}
```

---

# 🚀 TERRAFORM WORKFLOW (MODULE PROJECT)

```bash
terraform init
terraform plan
terraform apply
```

Terraform:

* Loads module
* Passes variables
* Creates infra
* Returns outputs

---

# 🔁 MODULE VERSIONING (ADVANCED, INTERVIEW GOLD)

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.0"
}
```

👉 Real teams use **registry modules** + version pinning.

---

# 🧠 MODULE TYPES (INTERVIEW)

* **Root module** → main project
* **Child module** → reusable module

---

# ⚠️ COMMON MODULE MISTAKES

❌ Hardcoding values inside module
❌ No outputs
❌ Not documenting variables
❌ Too many responsibilities in one module

Rule:

> One module = one responsibility

---

# 🎯 INTERVIEW ONE-LINERS

* Modules improve reusability and maintainability
* Root module calls child modules
* Inputs via variables, outputs exposed
* Used for large-scale infrastructure

---

# 🏁 YOU’VE LEVELED UP

Now you can:

* Design Terraform projects
* Write reusable modules
* Explain real IaC architecture
* Impress interviewers

This is **mid-to-advanced Terraform knowledge** 💪

---
