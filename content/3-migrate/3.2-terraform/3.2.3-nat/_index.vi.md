---
title : "Tạo AWS NAT Gateway bằng cách sử dụng Terraform"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.2.3 </b> "
---

NAT Gateway được sử dụng trong các private subnet để cho phép service kết nối với internet. Đối với NAT, trước tiên cần allocate public IP address first.

    resource "aws_eip" "eip-vpc-main" {
      vpc = true
      tags = {
        Name = "eip-vpc-main"
      }
    }

    resource "aws_nat_gateway" "nat-gateway-vpc-main" {
      allocation_id = aws_eip.eip-vpc-main.id
      subnet_id     = aws_subnet.public-subnet-vpc-main-1.id
      connectivity_type = "public"
      tags = {
        Name = "nat-gateway-vpc-main"
      }
      # Internet Gateway must be created before aws_nat_gateway
      depends_on = [aws_internet_gateway.internet-gateway-vpc-main]
    }
