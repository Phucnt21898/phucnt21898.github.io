---
title : "Create Internet Gateway in AWS using Terraform"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---

To provide internet access for my services. Need to create internet gateway in the VPC, It will be used as a default route in public subnets

    resource "aws_internet_gateway" "internet-gateway-vpc-main" {
      vpc_id = aws_vpc.vpc-main.id
      tags = {
        Name = "internet-gateway-vpc-main"
      }
    }

