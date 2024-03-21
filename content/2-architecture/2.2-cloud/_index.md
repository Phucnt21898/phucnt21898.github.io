---
title : "System architecture when deployed to AWS EKS cluster"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
![eks-cluster](/images/002-architect/eks-cluster.png)

This is Infrastructure on the AWS cloud environment when migrated from On-premises. On the AWS Cloud environment, AWS is responsible for managing the Control Plane instead of us having to manage the Control Plane ourselves, ensuring High Availability and backup & restore scenarios when incidents occur on the On-premises environment. Reduces the burden of Infrastructure management on us and helps us focus on application development.

On the AWS Cloud environment, users will access the Phonebook Service using a domain that is resolved using AWS Route53. Then the request will go to the AWS Load balancer which is managed by the AWS ALB Ingress Controller instead of going through the Nginx Ingress Controller on the On-premises environment, then the request will go directly to the Pods that are deployed on the Workers node
The DevOps team will manage the AWS EKS cluster by performing actions on the Bastion Host. The DevOps team will access the Bastion host via AWS Instance Connect

**We will conduct Migrate services from On-premises environment to AWS cloud environment according to the following steps:**
  1. First, we will create a Bastion host and install the necessary tools to perform apply Terraform code and manage AWS EKS cluster.

  2. Next, we will build Infrastructure on the AWS Cloud environment (Using the Infrastructure as code tool Terraform)

  3. Next, we will deploy the architecture of the Phonebook App using Kubernetes platform on the EKS cluster similar to the architecture of the Phonebook App on the On-premises environment (At this time, the company's services have been deployed to the EKS cluster, but no data yet)
  
  4. Finally, we will deploy backup and restore App data on On-premises environment to EKS cluster
