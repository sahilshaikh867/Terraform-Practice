---

# 🧱 `variables.tf` — INPUTS TO TERRAFORM

## 🧠 What is `variables.tf`?

> It defines **input variables** so your Terraform code is **reusable and configurable**.

Instead of hardcoding values → you pass them in.

---

## 📄 Example: `variables.tf`

```hcl
variable "region" {
  description = "AWS region"
  type        = string
  default     = "ap-south-1"
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}
```

---

## 🔗 Using variables in `main.tf`

```hcl
provider "aws" {
  region = var.region
}

resource "aws_instance" "web" {
  ami           = "ami-0abcdef123"
  instance_type = var.instance_type
}
```

---

## 🧠 Why variables matter (INTERVIEW LINE)

> Variables make Terraform code **dynamic, reusable, and environment-independent**.

---

## 📥 Ways to pass variables

```bash
terraform apply -var="instance_type=t3.micro"
```

or

```bash
terraform apply -var-file="dev.tfvars"
```

---

# 📤 `outputs.tf` — OUTPUTS FROM TERRAFORM

## 🧠 What is `outputs.tf`?

> It displays **useful information** after infrastructure is created.

Example:

* Public IP
* DNS name
* Resource ID

---

## 📄 Example: `outputs.tf`

```hcl
output "instance_public_ip" {
  description = "Public IP of EC2 instance"
  value       = aws_instance.web.public_ip
}
```

After apply:

```bash
terraform apply
```

Output:

```text
instance_public_ip = 13.235.xxx.xxx
```

---

## 🧠 Why outputs matter (INTERVIEW LINE)

> Outputs help share infrastructure details with users or other tools.

---

## 🔄 Outputs used by other tools

* CI/CD pipelines
* Ansible inventory
* Shell scripts

---

# 🔁 COMPLETE SIMPLE TERRAFORM FLOW

```text
variables.tf   → inputs
main.tf        → resources
outputs.tf     → results
```

---

## 🧠 COMMON MISTAKES

❌ Hardcoding values in `main.tf`
❌ Forgetting to expose useful outputs
❌ Naming outputs vaguely

---

## 🎯 INTERVIEW RAPID FIRE

**Q:** Why separate variables.tf and outputs.tf?
**A:** For clean, modular, and reusable Terraform code.

---

## 🏁 YOU’RE DOING GREAT

You now understand:

* Input variables
* Output values
* Clean Terraform structure
* How interviewers expect it

---
