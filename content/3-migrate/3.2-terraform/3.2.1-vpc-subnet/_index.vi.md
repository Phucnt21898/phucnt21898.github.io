---
title : "Tạo AWS VPC & Subnets bằng cách sử dụng Terraform"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.2.1 </b> "
---

Bước tiếp theo là tạo virtual private cloud sử dụng "aws_vpc" trong Terraform. TCó một trường bắt buộc cần cung cấp, đó là kích thước mạng.

Sau đó, tạo 2 public subnets và 2 private subnets:

    # Create VPC
    resource "aws_vpc" "vpc-main" {
      cidr_block = "10.10.0.0/16"
      enable_dns_hostnames = true
      enable_dns_support = true
      tags = {
        Name = "vpc-main"
      }
    }

    output "aws_vpc" {
      value = aws_vpc.vpc-main.id
    }

    # Create Subnet
    resource "aws_subnet" "public-subnet-vpc-main-1" {
      vpc_id     = aws_vpc.vpc-main.id
      cidr_block = "10.10.1.0/24"
      availability_zone = "us-east-1a"
      map_public_ip_on_launch = true
      tags = {
        Name = "public-subnet-vpc-main-1"
      }
    }

    resource "aws_subnet" "public-subnet-vpc-main-2" {
      vpc_id     = aws_vpc.vpc-main.id
      cidr_block = "10.10.2.0/24"
      availability_zone = "us-east-1b"
      map_public_ip_on_launch = true
      tags = {
        Name = "public-subnet-vpc-main-2"
      }
    }

    resource "aws_subnet" "private-subnet-vpc-main-1" {
      vpc_id     = aws_vpc.vpc-main.id
      cidr_block = "10.10.3.0/24"
      availability_zone = "us-east-1a"

      tags = {
        Name = "private-subnet-vpc-main-1"
      }
    }

    resource "aws_subnet" "private-subnet-vpc-main-2" {
      vpc_id     = aws_vpc.vpc-main.id
      cidr_block = "10.10.4.0/24"
      availability_zone = "us-east-1b"

      tags = {
        Name = "private-subnet-vpc-main-2"
      }
    }
