---
title : "Problem"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
XYZ company has deployed their Services using the Kubernetes platform on an On-premises environment. However, after a while, the company realized operational problems, had to manage the infrastructure itself, needed to ensure High Availability for Control Plane, Etcd, etc., so it took a lot of time and human resources.

In that context, the DevOps team received a request from The Leadership about the company's special event, when it was predicted that the number of requests to Services would increase 3 times. The DevOps team has checked that it is difficult for current resources to meet the 3-times increase in requests during the company's special event. Therefore, in order to solve the problem of rapid and cost-effective expansion, the DevOps team must predict and make provisions for expansion needs, including purchasing additional servers, expanding network infrastructure and increasing storage. . This scaling process can be costly and time-consuming, not to mention that at the end of the event there will be a large amount of idle resources left unused.

Therefore, the DevOps team decided to Migrate services to the Cloud environment to get the following benefits:


- Scalable flexibility: EKS cloud allows you to scale resources as needed without having to invest in hardware and infrastructure. You can easily increase or decrease the number of cluster nodes to meet the workload. Flexible expansion helps you save costs and optimize resource usage.
- Simplified infrastructure management: EKS cloud eliminates the difficulty and work involved in managing Kubernetes infrastructure. AWS manages and maintains the entire infrastructure, including installing, configuring, scaling, and upgrading Kubernetes components. This helps reduce management burden and focus on application development.
- AWS service integration: EKS cloud integrates well with other AWS services and tools. You can use services such as Amazon EC2, Amazon RDS, Amazon S3, and Amazon DynamoDB to supplement your applications. This helps leverage database management, data storage and processing services, minimizing integration work and increasing system reliability.
- Flexible costs: Migrate from an On-premises environment to EKS cloud helps you switch from a pre-pay model (CAPEX) to a pay-as-you-use model (OPEX). Instead of having to invest in hardware and infrastructure, you only pay to use the service using a pay-as-you-go model. This helps you optimize costs and be flexible in application development and operations.


{{% notice info %}}
This Workshop focuses on demoing Migrate a simple Micro-service phonebook application deployed on a Kubernetes cluster in an On-premises environment to an AWS Cloud environment, as well as building an EKS cluster architecture using Infrastrure as code tool is Terraform. The Workshop does not cover CI/CD, monitoring, logging and integrating other AWS services such as RDS, Cloudwatch, etc.
{{% /notice %}}
