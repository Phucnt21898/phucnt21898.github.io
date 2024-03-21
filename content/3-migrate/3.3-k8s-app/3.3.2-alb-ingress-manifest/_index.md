---
title : "Create ALB Ingress manifest file"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.3.2 </b> "
---

Create Security Group for AWS ALB using Terraform

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

Then run command terraform apply. The ID of the security group will appear.

We will use this ID to assign the Ingress rule

Next, we need to create namepace for App-phonebook by command: 

      kubectl create ns phonebook

Create manifest file to define Ingress rule for web-server & result-server

Specify the properties of the AWS ALB

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