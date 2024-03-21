---
title : "Tạo file ALB Ingress manifest"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.3.2 </b> "
---

Tạo Security Group cho AWS ALB bằng cách sử dụng Terraform

      resource "aws_security_group" "alb-security-group" {
      name        = "alb-security-group"
      description = "Allow all inbound traffic and all outbound traffic"
      vpc_id      = aws_vpc.vpc-main.id

      ingress {
         from_port        = 0
         to_port          = 0
         protocol         = "-1"
         cidr_blocks      = ["0.0.0.0/0"]
         description = "Allow all traffic from VPC main"
      } 

      egress {
         from_port        = 0
         to_port          = 0
         protocol         = "-1"
         cidr_blocks      = ["0.0.0.0/0"]
         description = "Allow all traffic outbound to VPC main"
      }

      }

Sau đó chạy lệnh terraform apply. ID của security group sẽ xuất hiện

Chúng ta sẽ sử dụng ID này để assign cho Ingress rule

Tiếp theo, Chúng ta cần tạo namepace cho App-phonebook bằng câu lệnh sau: 

      kubectl create ns phonebook

Tạo manifest file để define Ingress rule cho web-server & result-server

Chỉ định các thuộc tính cho AWS ALB ingress

**For web-server:**

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
      name: web-server
      namespace: phonebook
      annotations:
         # Ingress Core Settings
         kubernetes.io/ingress.class: "alb"
         alb.ingress.kubernetes.io/scheme: internet-facing
         alb.ingress.kubernetes.io/target-type: ip
         alb.ingress.kubernetes.io/tags: app=phonebook
         alb.ingress.kubernetes.io/subnets: subnet-030ff556cb14be7d9, subnet-09b6fd037c314a022
         # Health Check Settings
         alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
         alb.ingress.kubernetes.io/healthcheck-port: traffic-port
         alb.ingress.kubernetes.io/healthcheck-path: /
         alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
         alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
         alb.ingress.kubernetes.io/success-codes: '200'
         alb.ingress.kubernetes.io/healthy-threshold-count: '2'
         alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'
         alb.ingress.kubernetes.io/security-groups: sg-0e21986c4e1c60ef8
      spec:
      ingressClassName: "alb"
      rules:
         - http:
            paths:
               - path: /
                  pathType: Prefix
                  backend:
                  service:
                     name: phonebook-service
                     port:
                        number: 80

**For result-server**

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
      name: result-server
      namespace: phonebook
      annotations:
         # Ingress Core Settings
         kubernetes.io/ingress.class: "alb"
         alb.ingress.kubernetes.io/scheme: internet-facing
         alb.ingress.kubernetes.io/target-type: ip
         alb.ingress.kubernetes.io/tags: app=phonebook
         alb.ingress.kubernetes.io/subnets: subnet-030ff556cb14be7d9, subnet-09b6fd037c314a022
         # Health Check Settings
         alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
         alb.ingress.kubernetes.io/healthcheck-port: traffic-port
         alb.ingress.kubernetes.io/healthcheck-path: /
         alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
         alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
         alb.ingress.kubernetes.io/success-codes: '200'
         alb.ingress.kubernetes.io/healthy-threshold-count: '2'
         alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'
         alb.ingress.kubernetes.io/security-groups: 	sg-0e21986c4e1c60ef8
      spec:
      ingressClassName: alb
      rules:
         - http:
            paths:
               - path: /
                  pathType: Prefix
                  backend:
                  service:
                     name: result-service
                     port:
                        number: 80