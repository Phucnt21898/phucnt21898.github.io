---
title : "Create IAM OIDC provider EKS using Terraform"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 3.2.6 </b> "
---

To manage permissions for the applications that deployed in Kubernetes. We can either attach policies to Kubernetes nodes directly. In that case, every pod will get the same access to AWS resources. Or we can create OpenID connect provider, which will allow granting IAM permissions based on the service account used by the pod.

OpenID Connect is an authentication protocol which allows to verify user identity when a user is trying to access a protected HTTPs end point. OIDC is an evolutionary development of ideas implemented earlier in OAuth and OpenID.

    data "tls_certificate" "eks" {
        url = aws_eks_cluster.phonebook-eks-cluster.identity[0].oidc[0].issuer 
    }

    resource "aws_iam_openid_connect_provider" "eks" {
      client_id_list  = ["sts.amazonaws.com"]
      thumbprint_list = [data.tls_certificate.eks.certificates[0].sha1_fingerprint]
      url             = aws_eks_cluster.phonebook-eks-cluster.identity[0].oidc[0].issuer
    }

We complete to create main.tf file, then we will run command to run Terraform code

**terraform init:** initializes a working directory and downloads the necessary provider plugins and modules and setting up the backend for storing your infrastructure's state

![???](/images/003.4-terraform/1.png)

**terraform plan:** creates a dry-run, determining what actions are necessary to achieve the desired state defined in the Terraform configuration files

![???](/images/003.4-terraform/2.png)

**terraform apply:** to deploy in AWS cloud

![???](/images/003.4-terraform/3.png)

Enter **yes**, then AWS resources begin to be created

![???](/images/003.4-terraform/4.png)

**We have completed to create Infrastructure in AWS cloud by Terraform (Infrastructure as code tool)**