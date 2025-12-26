
**TERRAFORM — Infrastructure as Code (IaC)**
Welcome to the layer where DevOps stops clicking and starts **engineering**.


# 🏗️ TERRAFORM IN A NUTSHELL (ONE LINE)

> **Terraform lets you create, change, and version infrastructure using code.**

Instead of clicking in AWS console → you **declare** what you want, Terraform makes it real.

---

## 🧠 WHY TERRAFORM EXISTS

Manual infra = pain:

* Click fatigue
* No version history
* No repeatability
* Easy to mess up

Terraform gives:

* Declarative infra
* Version control
* Idempotency (safe re-runs)
* Multi-cloud support

---

## 🧱 CORE TERRAFORM CONCEPTS (MUST KNOW)

| Term     | Meaning                         |
| -------- | ------------------------------- |
| Provider | Cloud/service (AWS, Azure, GCP) |
| Resource | Actual infra (EC2, S3, VPC)     |
| State    | Tracks real infra               |
| Plan     | Preview of changes              |
| Apply    | Create/update infra             |
| Destroy  | Delete infra                    |

---

## 📄 BASIC TERRAFORM FILE STRUCTURE

```text
terraform-project/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfstate
```

Terraform files use **HCL** (`.tf`).

---

## 🧩 SIMPLE EXAMPLE (AWS EC2)

### main.tf

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0abcdef123"
  instance_type = "t2.micro"
}
```

---

## 🚀 TERRAFORM WORKFLOW (INTERVIEW FAVORITE)

Always in this order 👇

```bash
terraform init     # initialize project
terraform plan     # preview changes
terraform apply    # create infra
terraform destroy  # delete infra
```

👉 **init → plan → apply**

Say this confidently in interview.

---

## 🧠 TERRAFORM STATE (VERY IMPORTANT)

* Stored in `terraform.tfstate`
* Maps **code ↔ real infra**
* Never edit manually ❌

### Remote State (prod)

* S3 + DynamoDB (AWS)
* Prevents conflicts
* Enables team work

---

## 🔁 VARIABLES (CLEAN CODE)

### variables.tf

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

Use:

```hcl
instance_type = var.instance_type
```

---

## 📤 OUTPUTS

```hcl
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

---

## 🔄 IDPOTENCY (KEY CONCEPT)

> Running Terraform multiple times **won’t duplicate resources**.

Terraform checks state first → then acts.

---

## 🧠 TERRAFORM vs ANSIBLE (INTERVIEW GOLD)

| Terraform       | Ansible           |
| --------------- | ----------------- |
| Provision infra | Configure servers |
| Declarative     | Procedural        |
| Cloud-focused   | OS-focused        |
| State-based     | Agentless         |

**One-liner:**

> Terraform creates infrastructure, Ansible configures it.

---

## ⚠️ COMMON TERRAFORM MISTAKES

❌ Editing state file
❌ Hardcoding secrets
❌ Not using remote state
❌ Skipping `plan`

---

## 🎯 TERRAFORM INTERVIEW ONE-LINERS

* Terraform is declarative IaC tool
* Uses state to track infrastructure
* Supports multi-cloud
* Enables versioned, repeatable infra

---

## 🏁 TERRAFORM STATUS

You now understand:

* What Terraform is
* How it works
* Core commands
* State & workflow
* Interview expectations
