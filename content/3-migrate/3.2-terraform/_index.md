---
title : "Build Infrastucture on AWS Cloud environment using Terraform"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2. </b> "
---

Let's start with terraform. First, we need to create an AWS provider. It allows to interact with the many resources supported by AWS, such as VPC, EC2, EKS, and many others. You must configure the provider with the proper credentials before using it. 

In this case, I use Access Key & Secret Key to configure credentials for Terraform

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
  - [Create AWS VPC & Subnets using Terraform](3.2.1-vpc-subnet/)
  - [Create Internet Gateway in AWS using Terraform](3.2.2-igw/)
  - [Create NAT Gateway in AWS using Terraform](3.2.3-nat/)
  - [Create Route Table in AWS using Terraform](3.2.4-routetable/)
  - [Create EKS cluster using Terraform](3.2.5-eks-cluster/)
  - [Create IAM OIDC provider EKS using Terraform](3.2.6-oidc/)
  - [Access to EKS cluster](3.2.7-access-eks-cluster/)


