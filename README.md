
# Mini Project: Terraform Modules - VPC and S3 Bucket with Backend Storage

---

## 🎯 Purpose

In this project, you'll create **modularized Terraform configurations** to build:
- A reusable **Amazon VPC module**
- A reusable **S3 bucket module**
- Terraform **backend configuration** using an S3 bucket

---

## 🧠 Objectives

### ✅ Terraform Modules
- Learn how to write and use Terraform modules for reusable infrastructure.

### ✅ VPC Creation
- Build a Terraform module for creating a VPC with customizable configurations.

### ✅ S3 Bucket Creation
- Build a Terraform module for creating an S3 bucket with customizable options.

### ✅ Backend State Configuration
- Configure Terraform to use an S3 bucket as the **remote backend** for state storage.

---

## 🛠 Project Tasks

---

### Task 1: VPC Module

1. **Create the project structure:**

   ```bash
   mkdir -p terraform-modules-vpc-s3/modules/vpc
   cd terraform-modules-vpc-s3
   ```

2. **Write the VPC module:**

   `modules/vpc/main.tf`

   ```hcl
   variable "vpc_cidr" {
     default = "10.0.0.0/16"
   }

   resource "aws_vpc" "main" {
     cidr_block           = var.vpc_cidr
     enable_dns_support   = true
     enable_dns_hostnames = true

     tags = {
       Name = "MyVPC"
     }
   }

   output "vpc_id" {
     value = aws_vpc.main.id
   }
   ```

---

### Task 2: S3 Bucket Module

1. **Create the S3 module directory:**

   ```bash
   mkdir -p modules/s3
   ```

2. **Write the S3 bucket module:**

   `modules/s3/main.tf`

   ```hcl
   variable "bucket_name" {}
   variable "acl" {
     default = "private"
   }

   resource "aws_s3_bucket" "bucket" {
     bucket = var.bucket_name
     acl    = var.acl

     tags = {
       Name = "MyS3Bucket"
     }
   }

   output "bucket_name" {
     value = aws_s3_bucket.bucket.id
   }
   ```

---

### Task 3: Backend Storage Configuration

1. **Create the main configuration file:**

   `main.tf`

   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   module "vpc" {
     source   = "./modules/vpc"
     vpc_cidr = "10.1.0.0/16"
   }

   module "s3_bucket" {
     source      = "./modules/s3"
     bucket_name = "your-unique-s3-bucket-name"
     acl         = "private"
   }
   ```

2. **Create the backend configuration file:**

   `backend.tf`

   > Replace placeholder values with your actual S3 bucket and DynamoDB lock table

   ```hcl
   terraform {
     backend "s3" {
       bucket         = "your-terraform-state-bucket"
       key            = "terraform.tfstate"
       region         = "us-east-1"
       encrypt        = true
       dynamodb_table = "your-lock-table"
     }
   }
   ```

---

## 🧪 Usage Instructions

1. **Initialize the project:**

   ```bash
   terraform init
   ```

2. **Apply the configuration:**

   ```bash
   terraform apply
   ```

   - Confirm the plan
   - Terraform will create both the **VPC** and **S3 bucket**

---

## 🔍 Observations

- Both VPC and S3 bucket modules were successfully invoked using `module` blocks.
- State was securely stored in the configured S3 backend.
- Backend configuration prevented state drift and ensured consistency.

---

## ⚠️ Challenges Faced

- Backend S3 bucket **must already exist** before running `terraform init`. If it doesn’t, create it manually using the AWS Console or CLI.
- Bucket name must be **globally unique** across AWS.
- DynamoDB lock table must exist if using `dynamodb_table` for state locking.

---

## 📌 Side Notes

- Run `aws configure` beforehand to ensure credentials are set.
- For production: add `variables.tf`, `outputs.tf`, and `README.md` to each module.
- Always test module inputs by modifying values in `main.tf`.

---

**End of Project**
```
