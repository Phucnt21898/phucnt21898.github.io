---
title : "Create Route Table in AWS using Terraform"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 3.2.4 </b> "
---

A route table contains a set of rules, called routes, that determine where network traffic from your subnet or gateway is directed

**Route Table for Public Subnets**

    resource "aws_route_table" "route-table-public-vpc-main" {
      vpc_id = aws_vpc.vpc-main.id

      route {
        cidr_block = "0.0.0.0/0"
        gateway_id = aws_internet_gateway.internet-gateway-vpc-main.id
      }
      
      tags = {
        Name = "route-table-public-vpc-main"
      }
    }

    resource "aws_route_table_association" "public-subnet-1" {
      subnet_id      = aws_subnet.public-subnet-vpc-main-1.id
      route_table_id = aws_route_table.route-table-public-vpc-main.id
    }
    resource "aws_route_table_association" "public-subnet-2" {
      subnet_id      = aws_subnet.public-subnet-vpc-main-2.id
      route_table_id = aws_route_table.route-table-public-vpc-main.id
    }


**Route Table for Private Subnets**

    resource "aws_route_table" "route-table-private-vpc-main" {
      vpc_id = aws_vpc.vpc-main.id

      route {
        cidr_block = "0.0.0.0/0"
        nat_gateway_id = aws_nat_gateway.nat-gateway-vpc-main.id
      }
      
      tags = {
        Name = "route-table-private-vpc-main"
      }
    }
    resource "aws_route_table_association" "private-subnet-1" {
      subnet_id      = aws_subnet.private-subnet-vpc-main-1.id
      route_table_id = aws_route_table.route-table-private-vpc-main.id
    }
    resource "aws_route_table_association" "private-subnet-2" {
      subnet_id      = aws_subnet.private-subnet-vpc-main-2.id
      route_table_id = aws_route_table.route-table-private-vpc-main.id
    }