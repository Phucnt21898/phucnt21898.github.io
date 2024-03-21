---
title : "Truy cập vào cụm EKS"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 3.2.7 </b> "
---

Để có thể truy cập vào cụm EKS thì ta cần cài đặt **_aws-iam-authenticator_** trên bastion host, sau đó update kubeconfig
AWS IAM Authenticator là một command-line tool giúp quản lý xác thực truy cập cụm cho Amazon Elastic Container Service for Kubernetes (EKS)

Bằng cách tận dụng IAM Authenticator, người dùng có thể xác thực dựa trên AWS IAM credentials và sử dụng liền mạch các khả năng mạnh mẽ của EKS. Điều này giúp đơn giản hóa quy trình xác thực và giúp duy trì môi trường an toàn và dễ quản lý cho các cụm Kubernetes.

        # Download binary file
    curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.15.10/2020-02-22/bin/linux/amd64/aws-iam-authenticator
        
        # Add EXECUTE permission
    chmod +x ./aws-iam-authenticator
       
        # Move binary file to folder bin
    sudo mv ./aws-iam-authenticator /usr/local/bin
        
Tiếp theo, update kubeconfig bằng command:

    aws eks update-kubeconfig --name phonebook-eks-cluster

Sau khi chạy câu lệnh trên, kubeconfig file được update trong folder /root/.kube/config. Chúng ta giờ đây có thể quản lý cụm EKS bằng kubectl tool

![???](/images/003.4-terraform/6.png)
