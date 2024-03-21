---
title : "AWS ALB Ingress Controller"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.3.1 </b> "
---

Tại thời điểm này, Ứng dụng Phonebook đã được triển khai trong private subnet, vì vậy nếu cần đưa Ứng dụng ra Internet, chúng ta cần triển khai bộ cân bằng tải AWS trong public subnet trỏ tới App Phonebook trong private subnet.

Amazon Application Load Balancer (ALB) là một dịch vụ AWS phổ biến giúp cân bằng tải lưu lượng truy cập đến ở lớp ứng dụng (layer 7) trên nhiều target, chẳng hạn như phiên bản Amazon EC2, trong một khu vực. ALB hỗ trợ nhiều tính năng bao gồm định tuyến dựa trên máy chủ hoặc đường dẫn, chấm dứt TLS (Transport Layer Security), WebSockets, HTTP/2, tích hợp AWS WAF (Web Application Firewall), tích hợp logs và healthy check.

Kubernetes Ingress là tài nguyên API cho phép bạn quản lý quyền truy cập HTTP(S) bên ngoài hoặc nội bộ vào các dịch vụ Kubernetes chạy trong một cụm

ALB Ingress Controller là mã nguồn mở kích hoạt việc tạo ALB và các tài nguyên AWS hỗ trợ cần thiết bất cứ khi nào người dùng Kubernetes khai báo tài nguyên Ingress trong cụm. Tài nguyên Ingress sử dụng ALB để định tuyến lưu lượng HTTP(S) đến các điểm cuối khác nhau trong cụm. Ingress Controller Ingress AWS ALB hoạt động trên mọi cụm Kubernetes, bao gồm cả Amazon Elastic Kubernetes Service (Amazon EKS).

**Cách thức hoạt động của AWS Load Balancer controller:**

![???](/images/003.5-k8s-app/1.png)

**[1]:** Ingress Controller theo dõi các sự kiện Ingress từ API server. Khi tìm thấy tài nguyên Ingress đáp ứng yêu cầu của mình, nó sẽ bắt đầu tạo tài nguyên AWS.

**[2]:** ALB (ELBv2) được tạo trong AWS cho Ingress resource mới. ALB này có thể truy cập internet hoặc nội bộ. Bạn cũng có thể chỉ định các mạng con được tạo bằng cách sử dụng chú thích.

**[3]:** Nhóm mục tiêu được tạo trong AWS cho từng dịch vụ Kubernetes duy nhất được mô tả trong tài nguyên đầu vào.

**[4]:** Trình nghe được tạo cho mọi cổng được nêu chi tiết trong chú thích tài nguyên đầu vào của bạn. Khi không có cổng nào được chỉ định, các giá trị mặc định hợp lý (80 hoặc 443) sẽ được sử dụng. Chứng chỉ cũng có thể được đính kèm thông qua chú thích.

**[5]:** Các quy tắc được tạo cho từng đường dẫn được chỉ định trong Ingress resource của bạn. Điều này đảm bảo lưu lượng truy cập đến một đường dẫn cụ thể được chuyển đến Dịch vụ Kubernetes chính xác.


Ngoài những điều trên, Ingress Controller còn...
- Xóa các thành phần AWS khi Ingress resource bị xóa khỏi k8.
- Sửa đổi các thành phần AWS khi Ingress resource thay đổi theo k8.
- Tập hợp danh sách các thành phần AWS liên quan đến hoạt động Ingress hiện có khi khởi động, cho phép bạn khôi phục nếu Ingress Controller được khởi động lại.

Ingress Controller Cân bằng tải AWS hỗ trợ hai chế độ lưu lượng:
- **Chế độ Instance:** Lưu lượng truy cập bắt đầu tại ALB và đến các nút Kubernetes thông qua NodePort của mỗi dịch vụ. Điều này có nghĩa là các dịch vụ được tham chiếu từ Ingress resource phải được hiển thị theo type:NodePort để ALB có thể tiếp cận.
- **Chế độ IP:** Lưu lượng truy cập bắt đầu tại ALB và đến trực tiếp các nhóm Kubernetes. CNI phải hỗ trợ ip POD có thể truy cập trực tiếp qua địa chỉ IP phụ trên ENI (Tham khảo: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)
Theo mặc định, chế độ Phiên bản được sử dụng, người dùng có thể chọn chế độ này một cách rõ ràng thông qua chú thích alb.ingress.kubernetes.io/target-type.


**Cài đặt AWS ALB Ingress Controller:**

**Step 1:** Tạo Kubernetes RBAC role & Service Account cho ALB Ingress Controller.

Tạo manifest file để tạo permission cho aws-load-balancer-controller, sau đó apply nó

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


**Step 2.** Tạo IAM Policy cho ALB Ingress Controller

Chính sách IAM này sẽ cho phép ALB Controller Ingress thực hiện lệnh gọi tới API AWS

**Step 2.1:** Go to IAM Service: https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1

**Step 2.2:** Select Policies, then click to Create Policies:
![???](/images/003.5-k8s-app/2.png)

**Step 2.3:** Nhập policy for ALB Ingress Controller như sau:

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


**Step 2.4:** Click vào **Create Policy**


**Step 3:**  Tạo IAM role cho ALB Ingress Controller sau đó attach role đến service account

      eksctl create iamserviceaccount \
         --region us-east-1 \
         --name aws-load-balancer-controller \
         --namespace kube-system \
         --cluster phonebook-eks-cluster \
         --attach-policy-arn arn:aws:iam::413403005747:policy/ALBIngressControllerIAMPolicy \
         --override-existing-serviceaccounts \
         --approve

**Step 4:** Cài đặt cert-manager

cert-manager là một addon Kubernetes để tự động hóa việc quản lý và cấp Cert TLS từ nhiều nguồn phát hành khác nhau. Nó sẽ đảm bảo các chứng chỉ có giá trị và được cập nhật định kỳ, đồng thời cố gắng gia hạn chứng chỉ vào thời điểm thích hợp trước khi hết hạn.

      kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.12.3/cert-manager.yaml

**Step 5:** Triển khai ALB Ingress Controller

Download the manifest file for the ALB Ingress Controller

      wget https://github.com/kubernetes-sigs/aws-load-balancer-controller/releases/download/v2.7.0/v2_7_0_full.yaml

Chỉnh sửa tệp yaml đã lưu, đi tới Deployment spec và set controller giá trị --cluster-name arg thành EKS cluster

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

Nếu bạn sử dụng IAM role cho service account, Nên xóa ServiceAccount khỏi thông số yaml. Nếu xóa phần cài đặt khỏi thông số yaml, việc xóa ServiceAccount sẽ giữ nguyên eksctl đã tạo iamserviceaccoun
Áp dụng tệp yaml:

      kubectl apply -f v2_7_0_full.yaml

Ta đã cài đặt thành công AWS ALB Ingress Controller:
![???](/images/003.5-k8s-app/3.png)

