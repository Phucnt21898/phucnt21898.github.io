---
title : "Access to EKS cluster"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 3.2.7 </b> "
---

To access the EKS cluster, you need to install **_aws-iam-authenticator_** on the bastion host, then update kubeconfig
AWS IAM Authenticator for Kubernetes is a command-line tool that helps manage cluster access authentication for Amazon Elastic Container Service for Kubernetes (EKS)

By leveraging IAM Authenticator, users can authenticate against their AWS IAM credentials and seamlessly utilize the powerful capabilities of EKS. This simplifies the authentication process and helps maintain a secure and manageable environment for Kubernetes clusters.

        # Download binary file
    curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.15.10/2020-02-22/bin/linux/amd64/aws-iam-authenticator
        
        # Add EXECUTE permission
    chmod +x ./aws-iam-authenticator
       
        # Move binary file to folder bin
    sudo mv ./aws-iam-authenticator /usr/local/bin
        
Next, update kubeconfig by command:

    aws eks update-kubeconfig --name phonebook-eks-cluster

After running command above, kubeconfig file was updated in part /root/.kube/config. We can use kubectl tool to manage EKS cluster
![???](/images/003.4-terraform/6.png)
