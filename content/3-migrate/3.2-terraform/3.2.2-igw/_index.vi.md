---
title : "Tạo AWS Internet Gateway bằng cách sử dụng Terraform"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---

Để cung cấp truy cập internet cho các service. Cần internet gateway trong VPC, Nó sẽ được sử dụng làm tuyến mặc định trong các public subnet

    resource "aws_internet_gateway" "internet-gateway-vpc-main" {
      vpc_id = aws_vpc.vpc-main.id
      tags = {
        Name = "internet-gateway-vpc-main"
      }
    }

