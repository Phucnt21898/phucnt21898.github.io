---
title : "Create EKS cluster using Terraform"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 3.2.5 </b> "
---

Kubernetes clusters managed by Amazon EKS make calls to other AWS services on your behalf to manage the resources that we use with the service. For example, EKS will create an autoscaling group for each instance group if you use managed nodes. 

Before creating Amazon EKS clusters, we must create an IAM role with the AmazonEKSClusterPolicy.

    resource "aws_iam_role" "eks-cluster-role" {
      name = "eks-cluster-role"

      assume_role_policy = <<POLICY
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "Service": "eks.amazonaws.com"
          },
          "Action": "sts:AssumeRole"
        }
      ]
    }
    POLICY
    }

    resource "aws_iam_role_policy_attachment" "eks-cluster-role-attach-AmazonEKSClusterPolicy" {
      policy_arn = "arn:aws:iam::aws:policy/AmazonEKSClusterPolicy"
      role       = aws_iam_role.eks-cluster-role.name
    }

    resource "aws_eks_cluster" "phonebook-eks-cluster" {
      name     = "phonebook-eks-cluster"
      role_arn = aws_iam_role.eks-cluster-role.arn

      vpc_config {
        subnet_ids = [
          aws_subnet.private-subnet-vpc-main-1.id,
          aws_subnet.private-subnet-vpc-main-2.id,
          aws_subnet.public-subnet-vpc-main-1.id,
          aws_subnet.public-subnet-vpc-main-2.id
        ]
      }

      tags = {
        App = "phonebook"
      }

      depends_on = [aws_iam_role_policy_attachment.eks-cluster-role-attach-AmazonEKSClusterPolicy]
    }


Next, we will create a Node Group for EKS cluster. However, before creating a Node Group we need to attach the necessary policies to its Role

    resource "aws_iam_role" "node-group-role" {
      name = "node-group-role"

      assume_role_policy = jsonencode({
        Statement = [{
          Action = "sts:AssumeRole"
          Effect = "Allow"
          Principal = {
            Service = "ec2.amazonaws.com"
          }
        }]
        Version = "2012-10-17"
      })
    }

    resource "aws_iam_role_policy_attachment" "node-group-role-attach-AmazonEKSWorkerNodePolicy" {
      policy_arn = "arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy"
      role       = aws_iam_role.node-group-role.name
    }

    resource "aws_iam_role_policy_attachment" "node-group-role-attach-AmazonEKS_CNI_Policy" {
      policy_arn = "arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy"
      role       = aws_iam_role.node-group-role.name
    }

    resource "aws_iam_role_policy_attachment" "node-group-role-attach-AmazonEC2ContainerRegistryReadOnly" {
      policy_arn = "arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly"
      role       = aws_iam_role.node-group-role.name
    }

    resource "aws_iam_role_policy_attachment" "node-group-role-attach-AmazonS3FullAccess" {
      policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
      role       = aws_iam_role.node-group-role.name
    }

    resource "aws_iam_role_policy_attachment" "node-group-role-attach-AmazonEC2FullAccess" {
      policy_arn = "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
      role       = aws_iam_role.node-group-role.name
    }

Next, we write Terraform code to create Node Group with: \
    - Name of Node Group is "phonebook-nodes" into "phonebook-eks-cluster" EKS cluster \
    - Network is private-subnet-vpc-main-1 \
    - ami is Amazon Linux 2 x86_64, capacity type is on demand, instance type is t2.medium and storage disk is 10Gib \
    - scaling config: desired = 1, max = 1 & min = 1 

    resource "aws_eks_node_group" "phonebook-nodes" {
      cluster_name    = aws_eks_cluster.phonebook-eks-cluster.name
      node_group_name = "phonebook-nodes"
      node_role_arn   = aws_iam_role.node-group-role.arn

      subnet_ids = [
        aws_subnet.private-subnet-vpc-main-1.id
      ]

      # Node group compute configuration
      ami_type = "AL2_x86_64"
      capacity_type  = "ON_DEMAND"
      instance_types = ["t2.medium"]
      disk_size = 10

      scaling_config {
        desired_size = 1
        max_size     = 1
        min_size     = 1
      }
      
      # Node group update configuration
      update_config {
        max_unavailable = 1
      }

      labels = {
        app = "phonebook"
      }

      depends_on = [
        aws_iam_role_policy_attachment.node-group-role-attach-AmazonEKSWorkerNodePolicy,
        aws_iam_role_policy_attachment.node-group-role-attach-AmazonEKS_CNI_Policy,
        aws_iam_role_policy_attachment.node-group-role-attach-AmazonEC2ContainerRegistryReadOnly,
        aws_iam_role_policy_attachment.node-group-role-attach-AmazonS3FullAccess,
        aws_iam_role_policy_attachment.node-group-role-attach-AmazonEC2FullAccess
      ]
    }