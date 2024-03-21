---
title : "AWS ALB Ingress Controller"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.3.1 </b> "
---

At this time, app-phonebook has been deployed in Private Subnet, so if you need to expose App to the Internet, we need to deploy AWS Load balancer in Public Subnet pointing to App-phonebook in Private Subnet.

Amazon Application Load Balancer (ALB) is a popular AWS service that load balances incoming traffic at the application layer (layer 7) across multiple targets, such as Amazon EC2 instances, in a region. ALB supports multiple features including host or path based routing, TLS (Transport Layer Security) termination, WebSockets, HTTP/2, AWS WAF (Web Application Firewall) integration, integrated access logs, and health checks.

Kubernetes Ingress is an API resource that allows you manage external or internal HTTP(S) access to Kubernetes services running in a cluster

The open source AWS ALB Ingress controller triggers the creation of an ALB and the necessary supporting AWS resources whenever a Kubernetes user declares an Ingress resource in the cluster. The Ingress resource uses the ALB to route HTTP(S) traffic to different endpoints within the cluster. The AWS ALB Ingress controller works on any Kubernetes cluster including Amazon Elastic Kubernetes Service (Amazon EKS).

**How AWS Load Balancer controller works:**

![???](/images/003.5-k8s-app/1.png)

**[1]:** The controller watches for ingress events from the API server. When it finds ingress resources that satisfy its requirements, it begins the creation of AWS resources.

**[2]:** An ALB (ELBv2) is created in AWS for the new ingress resource. This ALB can be internet-facing or internal. You can also specify the subnets it's created in using annotations.

**[3]:** Target Groups are created in AWS for each unique Kubernetes service described in the ingress resource.

**[4]:** Listeners are created for every port detailed in your ingress resource annotations. When no port is specified, sensible defaults (80 or 443) are used. Certificates may also be attached via annotations.

**[5]:** Rules are created for each path specified in your ingress resource. This ensures traffic to a specific path is routed to the correct Kubernetes Service.


Along with the above, the controller also...
- deletes AWS components when ingress resources are removed from k8s.
- modifies AWS components when ingress resources change in k8s.
- assembles a list of existing ingress-related AWS components on start-up, allowing you to recover if the controller were to be restarted.

AWS Load Balancer controller supports two traffic modes:
- **Instance mode:** Ingress traffic starts at the ALB and reaches the Kubernetes nodes through each service's NodePort. This means that services referenced from ingress resources must be exposed by type:NodePort in order to be reached by the ALB.
- **IP mode:** Ingress traffic starts at the ALB and reaches the Kubernetes pods directly. CNIs must support directly accessible POD ip via secondary IP addresses on ENI (Ref: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)
By default, Instance mode is used, users can explicitly select the mode via alb.ingress.kubernetes.io/target-type annotation.


**Install AWS ALB Ingress Controller:**

**Step 1:** Create Kubernetes RBAC role & Service Account for ALB Ingress Controller.

We will create manifest file to create permission for aws-load-balancer-controller, then apply it:

      cat <<EOF > rbac-role-aws-load-balancer-controller.yaml
      ---
      apiVersion: rbac.authorization.k8s.io/v1
      kind: ClusterRole
      metadata:
      labels:
         app.kubernetes.io/name: aws-load-balancer-controller
      name: aws-load-balancer-controller
      rules:
      - apiGroups:
            - ""
            - extensions
         resources:
            - configmaps
            - endpoints
            - events
            - ingresses
            - ingresses/status
            - services
            - pods/status
         verbs:
            - create
            - get
            - list
            - update
            - watch
            - patch
      - apiGroups:
            - ""
            - extensions
         resources:
            - nodes
            - pods
            - secrets
            - services
            - namespaces
         verbs:
            - get
            - list
            - watch
      ---
      apiVersion: rbac.authorization.k8s.io/v1
      kind: ClusterRoleBinding
      metadata:
      labels:
         app.kubernetes.io/name: aws-load-balancer-controller
      name: aws-load-balancer-controller
      roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: aws-load-balancer-controller
      subjects:
      - kind: ServiceAccount
         name: aws-load-balancer-controller
         namespace: kube-system
      ---
      apiVersion: v1
      kind: ServiceAccount
      metadata:
      labels:
         app.kubernetes.io/name: aws-load-balancer-controller
      name: aws-load-balancer-controller
      namespace: kube-system
      ...
      EOF

      kubectl apply -f rbac-role-aws-load-balancer-controller.yaml


**Step 2.** Create IAM Policy for ALB Ingress Controller

This IAM policy will allow our ALB Ingress Controller pod to make calls to AWS APIs

**Step 2.1:** Go to IAM Service: https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1

**Step 2.2:** Select Policies, then click to Create Policies:
![???](/images/003.5-k8s-app/2.png)

**Step 2.3:** Enter policy for ALB Ingress Controller as bellow:

      {
         "Version": "2012-10-17",
         "Statement": [
            {
                  "Effect": "Allow",
                  "Action": [
                     "acm:DescribeCertificate",
                     "acm:ListCertificates",
                     "acm:GetCertificate",
                     "ec2:*"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "ec2:AuthorizeSecurityGroupIngress",
                     "ec2:CreateSecurityGroup",
                     "ec2:CreateTags",
                     "ec2:DeleteTags",
                     "ec2:DeleteSecurityGroup",
                     "ec2:DescribeAccountAttributes",
                     "ec2:DescribeAddresses",
                     "ec2:DescribeInstances",
                     "ec2:DescribeInstanceStatus",
                     "ec2:DescribeInternetGateways",
                     "ec2:DescribeNetworkInterfaces",
                     "ec2:DescribeSecurityGroups",
                     "ec2:DescribeSubnets",
                     "ec2:DescribeTags",
                     "ec2:DescribeVpcs",
                     "ec2:ModifyInstanceAttribute",
                     "ec2:ModifyNetworkInterfaceAttribute",
                     "ec2:RevokeSecurityGroupIngress"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "elasticloadbalancing:AddListenerCertificates",
                     "elasticloadbalancing:AddTags",
                     "elasticloadbalancing:CreateListener",
                     "elasticloadbalancing:CreateLoadBalancer",
                     "elasticloadbalancing:CreateRule",
                     "elasticloadbalancing:CreateTargetGroup",
                     "elasticloadbalancing:DeleteListener",
                     "elasticloadbalancing:DeleteLoadBalancer",
                     "elasticloadbalancing:DeleteRule",
                     "elasticloadbalancing:DeleteTargetGroup",
                     "elasticloadbalancing:DeregisterTargets",
                     "elasticloadbalancing:DescribeListenerCertificates",
                     "elasticloadbalancing:DescribeListeners",
                     "elasticloadbalancing:DescribeLoadBalancers",
                     "elasticloadbalancing:DescribeLoadBalancerAttributes",
                     "elasticloadbalancing:DescribeRules",
                     "elasticloadbalancing:DescribeSSLPolicies",
                     "elasticloadbalancing:DescribeTags",
                     "elasticloadbalancing:DescribeTargetGroups",
                     "elasticloadbalancing:DescribeTargetGroupAttributes",
                     "elasticloadbalancing:DescribeTargetHealth",
                     "elasticloadbalancing:ModifyListener",
                     "elasticloadbalancing:ModifyLoadBalancerAttributes",
                     "elasticloadbalancing:ModifyRule",
                     "elasticloadbalancing:ModifyTargetGroup",
                     "elasticloadbalancing:ModifyTargetGroupAttributes",
                     "elasticloadbalancing:RegisterTargets",
                     "elasticloadbalancing:RemoveListenerCertificates",
                     "elasticloadbalancing:RemoveTags",
                     "elasticloadbalancing:SetIpAddressType",
                     "elasticloadbalancing:SetSecurityGroups",
                     "elasticloadbalancing:SetSubnets",
                     "elasticloadbalancing:SetWebAcl"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "iam:CreateServiceLinkedRole",
                     "iam:GetServerCertificate",
                     "iam:ListServerCertificates"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "cognito-idp:DescribeUserPoolClient",
                     "eks:*"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "waf-regional:GetWebACLForResource",
                     "waf-regional:GetWebACL",
                     "waf-regional:AssociateWebACL",
                     "waf-regional:DisassociateWebACL"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "tag:GetResources",
                     "tag:TagResources"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "waf:GetWebACL"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "wafv2:GetWebACL",
                     "wafv2:GetWebACLForResource",
                     "wafv2:AssociateWebACL",
                     "wafv2:DisassociateWebACL"
                  ],
                  "Resource": "*"
            },
            {
                  "Effect": "Allow",
                  "Action": [
                     "shield:DescribeProtection",
                     "shield:GetSubscriptionState",
                     "shield:DeleteProtection",
                     "shield:CreateProtection",
                     "shield:DescribeSubscription",
                     "shield:ListProtections"
                  ],
                  "Resource": "*"
            }
         ]
      }


**Step 2.4:** Click on **Create Policy**


**Step 3:**  Create an IAM role for the ALB Ingress Controller and attach the role to the service account

      eksctl create iamserviceaccount \
         --region us-east-1 \
         --name aws-load-balancer-controller \
         --namespace kube-system \
         --cluster phonebook-eks-cluster \
         --attach-policy-arn arn:aws:iam::413403005747:policy/ALBIngressControllerIAMPolicy \
         --override-existing-serviceaccounts \
         --approve

**Step 4:** Install cert-manager

cert-manager is a Kubernetes addon to automate the management and issuance of TLS certificates from various issuing sources. It will ensure certificates are valid and up to date periodically, and attempt to renew certificates at an appropriate time before expiry.

      kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.12.3/cert-manager.yaml

**Step 5:** Deploy ALB Ingress Controller

Download the manifest file for the ALB Ingress Controller

      wget https://github.com/kubernetes-sigs/aws-load-balancer-controller/releases/download/v2.7.0/v2_7_0_full.yaml

Edit the saved yaml file, go to the Deployment spec, and set the controller --cluster-name arg value to your EKS cluster name

      apiVersion: apps/v1
      kind: Deployment
      . . .
      name: aws-load-balancer-controller
      namespace: kube-system
      spec:
         . . .
         template:
            spec:
                  containers:
                     - args:
                        - --cluster-name=<your-cluster-name>

If you use IAM roles for service accounts, we recommend that you delete the ServiceAccount from the yaml spec. If you delete the installation section from the yaml spec, deleting the ServiceAccount preserves the eksctl created iamserviceaccoun
Apply the yaml file:  

      kubectl apply -f v2_7_0_full.yaml

We have successfully installed AWS ALB Ingress Controller:
![???](/images/003.5-k8s-app/3.png)

