---
title : "Xây dựng cơ sở hạ tầng trên AWS Cloud bằng Terraform"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2. </b> "
---

Bắt đầu với Terraform. Đầu tiên, chúng ta cần tạo provider AWS. Nó cho phép tương tác với nhiều tài nguyên được AWS hỗ trợ, chẳng hạn như VPC, EC2, EKS và nhiều tài nguyên khác. Cần phải định cấu hình nhà cung cấp với thông tin xác thực phù hợp trước khi sử dụng.

Trong trường hợp này, tôi sử dụng Access Key & Secret Key để định cấu hình thông tin xác thực cho Terraform

    provider "aws" { 
      region = "us-east-1" 
    }
    terraform {
      required_providers {
         aws = {
            source  = "hashicorp/aws"
            version = "~> 3.0"
         }
      }
    }



### Content:
  - [Tạo AWS VPC & Subnets bằng cách sử dụng Terraform](3.2.1-vpc-subnet/)
  - [Tạo AWS Internet Gateway bằng cách sử dụng Terraform](3.2.2-igw/)
  - [Tạo AWS NAT Gateway bằng cách sử dụng Terraform](3.2.3-nat/)
  - [Tạo AWS Route Table bằng cách sử dụng Terraform](3.2.4-routetable/)
  - [Tạo AWS EKS cluster bằng cách sử dụng Terraform](3.2.5-eks-cluster/)
  - [Tạo IAM OIDC provider EKS bằng cách sử dụng Terraform](3.2.6-oidc/)
  - [Truy cập vào cụm EKS](3.2.7-access-eks-cluster/)


